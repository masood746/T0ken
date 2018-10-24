# The Compliance Contract
The compliance contract uses compliance rules to regulate the transfer of tokens. The contract maintains and manages
the collection of rules which enforce SEC regulations and exemptions
(e.g. ([Reg A](reg-a) and [Reg D](reg-d)) during a trade between account types (custodian, broker, custodial-account,
or investor).
<span style="color:red">Is there a custodial and custodial-account?</span>

Rules are stored within buckets matching the sender's account type, providing flexibility, while preventing
unnecessary compliance checks for accounts that do not require it.

![Compliance Design Diagram][design]

The rest of this page annotates the code components for the token compliance contract
([`TokenCompliance.sol`](../../contracts/compliance/T0kenCompliance.sol)).

* **[Variables](#variables)**
    * [Global Constants](#globalconstants)
    * [Global Variables](#globalvariables)
* **[Modifiers](#modifiers)**
    * [affiliateNotFrozen](#modifieraffiliatenotfrozen)
    * [addressesNotFrozen](#moddifieraddressesnotfrozen)
* **[Getters](#getters)**
    * [getRules](#gettergetrules)
    * [canTransfer](#gettercantransfer)
    * [checkFrozen](#gettercheckfrozen)
* **[Setters](#setters)**
    * [setRules](#settersetrules)
    * [setStorage](#settersetstorage)
    * [setAffiliate](#settersetaffiliate)
    * [setAffiliateFreeze](#setAffiliateFreeze)    
    * [setAddressFrozen](#settersetaddressfrozen)


---
<a id="variables"></a>
### Variables

<a id="globalconstants"></a>
#### Global Constants
Parameter           | Type    | Visibility    | Value           | Description
------------------- | ------- | ------------- | --------------- | ----------------
`MAX_RULES`         | uint8   | public        | 25              | Maximum number of rules allowed
<span style="color:red">Is 25 max rule our choice or is it based on Ethereum constraints so that other third-parties have to abide by this number as well? Can 3rd-parties choose a different max rule number?
____

<a id="globalvariables"></a>
#### Global Variables
Parameter           | Type       | Visibility    | Value   | Description
------------------- | ---------- | ------------- | ------- | -----------------------
`store`             | Storage    | public        |      -   | An instance of the Storage contract
`affiliateFreeze`   | bool       | public        | true    | Indicates if all affiliates are frozen
`addressFreezes`    | mapping    | public        |    -     | `mapping(address => bool)`: List of addresses frozen for this token
`affiliates`        | mapping    | public        |     -    | `mapping(address => bool)`: List that holds the frozen status of the affiliates
`complianceRules`   | mapping    | private       |       -  | `mapping(uint8 => ComplianceRule[])`: Mapping from a rule number to the rule array

____

<a id="modifiers"></a>
### Modifiers
<a id="modifieraffiliatenotfrozen"></a>
`affiliateNotFrozen`:
> Checks the global variable `affiliateFreeze` to determine if all affiliates are Frozen.
> If all affiliates are frozen, checks the affiliate at `addr` is frozen.
> * If frozen, responds with "Affiliate is frozen."
>
>@param `addr address` - The address to check if its a frozen affiliate

<a id="moddifieraddressesnotfrozen"></a>
`addressesNotFrozen`:
> Checks to make sure both `to` and `from` addresses are not Frozen.
> * If `to` address is frozen, responds with "Receiver is frozen at the token."  
> * If `from` address is frozen, responds with "Sender is frozen at the token."
>
> @param to address - The recipient address to check
> @param from address - The sender address to check


-----
<a id="getters"></a>
### Getters

<a id="gettergetrules"></a>
`getRules`:
>  Returns all of the current compliance rules for this token.
>
> @param kind uint8 - The bucket of rules to get
> @return list address - A list of compliance rule addresses for the given bucket


<a id="gettercantransfer"></a>
`canTransfer`:
> Checks if a transfer can occur between the `from` and `to`addresses.
> Both addresses must be *valid*, *unfrozen*, and *pass all compliance rule checks for their respective types*.
>
> @param from address - The address of the sender
> @param to address - The address of the recipient
> @return bool - If a transfer can occur between the `from` and `to` addresses


<a id="gettercheckfrozen"></a>
`checkFrozen`:
> Checks the cascading frozen status of a given address.
>
> @param addr address - The address
> @return bool - The status of whether the address is frozen or not

-----
<a id="setters"></a>
### Setters

<a id="settersetrules"></a>
`setRules`:
>Replaces ALL of the existing rules with the ones provided.
> * If the number of rules provides is larger than MAX_RULES, responds with "To many rules".
>
>@param kind uint8 - The bucket of rules to set
>@param rules ComplianceRule[] - The new compliance rules to set
>

<a id="settersetstorage"></a>
`setStorage`:
> Sets the contract address for the Storage contract.
>
> @param s Storage - The Storage object


<a id="settersetaffiliate"></a>
`setAffiliate`:
>Sets an address affiliate status for this token.
>
>@param addr address - The address to set affiliate status for
>@param status bool - Whether the address is an affiliate or not

<a id="setAffiliateFreeze"></a>
`setAffiliateFreeze`:
> Sets this token frozen status for all affiliates.  
> @param freeze Whether affiliates are frozen.

<a id="settersetaddressfrozen"></a>
`setAddressFrozen`:
>Sets an address frozen status for this token.
>
>@param addr address - The address for which to update the frozen status
>@param freeze bool - The frozen status to set for the address


[design]: http://www.plantuml.com/plantuml/png/fLDFQy8m53-RJn7OPKCA-m15b6sU70Dri5jvfgzTQKmbBqwczBilKREqMV6odl9-V_B-l7HA1hJPRoLZEn0rbCWNrOPucYvH652bnCc4dzX8I23YRmS56uaE68rvSr2exIdBE7wXCiIpp8Ods93H84phzAZN6XGLg3Nczm-MJ-pd_EQAdqMEQPdFisX4tKbKaGCm3sP2Su7wlcUqcPllZhMkf1o-EzAsh6M-lJH9kbVrS6zdNX0JNQF3RWh2s9-QxKvMe0J-7Nwb3Ee2H5UrtF_sA43v6VjMXnpyT7DNippNI3JNpH5LRr7SJZjc7LoYSLNGDQGG3bRb5x1-h555POtvw-mZMxulNbRd28gYihJ1kKOyuWNp9JY4-Z8N9bpqfaDH2olJQ2WnL3lpxXZoUGFn__v7TrnyegZ33V6BY-R21jxBe2uS1sasHPMxF_m7
