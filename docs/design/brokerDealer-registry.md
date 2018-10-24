
# The Broker-Dealer Contract
A broker-dealer falls underneath of its managing custodian.
Only the broker-dealer's parent custodian may freeze, assign custodial-accounts to,
and remove a broker from the ecosystem.

##### Onchain Responsibilites

 - Onboarding of investors
 - Providing liquidity of dividends, tokens, etc.
 - Freezing, from performing any related actions, of an investor  
   _(for SEC regulatory purposes, or other reasons applicable by law)_
 - Management of onchain investor data:  
   _(the ability exists to add to, or include additional data as needed)_
   - Identification Hash
   - Accreditation Date
   - Domicile
 - Removal of investors

The Broker Dealer Registry contract implements the Broker Dealer Registry Interface for the tZero token

* [Libraries](#libraries)
* [Global Constants](#globalconstants)
* [Global Variables](#globalvariables)
* [Modifiers](#modifiers)
    * [onlyCustodian](#modifieronlycustodian)
    * [onlyNewAccount](#modifieronlynewaccount)
    * [onlyBrokerDealersCustodian](#modifieronlybrokerdealerscustodian)
    * [onlyAccountCustodian](#modifieronlyaccountscustodian)
* [Events](#events)
* [Getters](#getters)
* [Setters](#setters)
    * [setStorage](#settersetstorage)
    * [add](#setteradd)
    * [remove](#setterremove)
    * [addAccount](#setteraddaccount)
    * [removeAccount](#setterremoveaccount)
    * [setFrozen](#settersetfrozen)


____

<a id="libraries"></a>
##### Libraries

____

<a id="globalconstants"></a>
##### Global Constants
parameter                   | type    | visibility    | value           | description
--------------------------- | ------- | ------------- | --------------- | ----------------
`CUSTODIAN`                 | uint8   | private       | 1               |
`CUSTODIAL_ACCOUNT`         | uint8   | private       | 2               |
`BROKER_DEALER    `         | uint8   | private       | 3               |

____

<a id="globalvariables"></a>
##### Global Variables
parameter           | type       | visibility    | default | description
------------------- | ---------- | ------------- | ------- | -----------------------
`store`             | Storage    | public        |         |
____

<a id="modifiers"></a>
##### Modifiers
<a id="modifieronlycustodian"></a>
`onlyCustodian`:
> Checks that the message sender exists as a CUSTODIAN
> Checks that the custodian is not frozen.

<a id="modifieronlynewaccount"></a>
`onlyNewAccount`:
> Checks that the account does not already exist in store
>
> @param account address - the address to check

<a id="modifieronlybrokerdealerscustodian"></a>
`onlyBrokerDealersCustodian`:
> Checks that the `msg.sender` account exists as a CUSTODIAN.
> Checks that the `msg.sender` parent is a BROKER_DEALER
> Checks that the CUSTODIAN is not frozen
>
> @param brokerDealer address - The broker dealer address to check

<a id="modifieronlyaccountscustodian"></a>
`onlyAccountsCustodian`:
> Checks that the account exists as a CUSTODIAL_ACCOUNT
> Checks that `msg.sender` parent account (broker dealer) has a custodian parent
> Checks that the CUSTODIAN is not frozen
>
> @param account address - The custodial account address to check


____

<a id="events"></a>
##### Events

-----
<a id="getters"></a>
##### Getters

-----
<a id="setters"></a>
##### Setters

<a id="settersetstorage"></a>
`setStorage`:
> Sets the contract address for the Storage Contract
>
> @param s Storage - The Storage object


<a id="setteradd"></a>
`add`:
>  Adds a broker dealer to the registry
>  Upon successful addition, the contract must emit `Broker DealerAdded(custodian)`
>  THROWS if the address has already been added, or is zero
>
>  @param brokerDealer address - The address of the brokerDealer


<a id="setterremove"></a>
`remove`:
>  Removes a broker dealer from the registry
>  Upon successful removal, the contract must emit `BrokerDealerRemoved(brokerDealer)`
>  THROWS if the address doesn't exist, or is zero
>
>  @param brokerDealer address - The address of the broker dealer

<a id="setteraddaccount"></a>
`addAccount`:
>  Adds an account for the broker-dealer, using the account as the destination address
>
>  @param brokerDealer The broker-dealer to add the account for
>  @param account The account, and destination, to add for the broker-dealer


<a id="setterremoveaccount"></a>
`removeAccount`:
>  Removes the account, along with destination, from the broker-dealer
>
>  @param account The account to remove



<a id="settersetfrozen"></a>
`setFrozen`:
>  Sets whether or not a broker-dealer is frozen
>  Upon status change, the contract must emit `BrokerDealerFrozen(brokerDealer, frozen, owner)`
>
>  @param brokerDealer - The broker dealer address that is being updated
>  @param frozen Whether or not the broker daeler is frozen
