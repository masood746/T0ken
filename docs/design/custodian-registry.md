
# The Custodian Contract

The financial institution that may hold a brokers or investor's securities for a variety of reasons
_(transfer, withdrawl, safekeeping)_.

A custodian may also perform settlements, interest payments, and dividend
collection/payments, depending on the type of security token offered.

##### Onchain Responsibilites

 - Onboarding of broker-dealers
 - Assignment of custodial-accounts of broker-dealers
 - Freezing, from performing any related actions, of a broker-dealer  
   _(for SEC regulatory purposes, or other reasons applicable by law)_
 - Freezing, from performing any related actions, of an investor  
   _(for SEC regulatory purposes, or other reasons applicable by law)_
 - Removal of broker-dealers


A custodial-account is managed by the custodian, added and bound to a broker-dealer, and used to hold custody of
tokens on behalf of an investor belonging to the broker-dealer.

These are simply holding accounts used for the sole purpose of performing transfer and withdrawals of tokens to, and
from, investors, to broker-dealers and custodians.

The Custodian Registry contract implements the Custodian Registry Interface for the tZero token

* [Libraries](#libraries)
* [Global Constants](#globalconstants)
* [Global Variables](#globalvariables)
* [Modifiers](#modifiers)
    * [isAllowed](#modifierisallowed)
* [Events](#events)
* [Getters](#getters)
* [Setters](#setters)
    * [setStorage](#setterssetstorage)
    * [setAffiliate](#setterssetaffiliate)
    * [setAddressFrozen](#setterssetaddressfrozen)
    * [setRules](#setterssetrules)


____

<a id="libraries"></a>
##### Libraries

____

<a id="globalconstants"></a>
##### Global Constants
parameter           | type    | visibility    | value           | description
------------------- | ------- | ------------- | --------------- | ----------------
`CUSTODIAN`         | uint8   | private       | 1               |

____

<a id="globalvariables"></a>
##### Global Variables
parameter           | type       | visibility    | default | description
------------------- | ---------- | ------------- | ------- | -----------------------
`store`             | Storage    | public        |         |
____

<a id="modifiers"></a>
##### Modifiers
<a id="modifierisallowed"></a>
`isAllowed`:
> Checks the CUSTODIAN permission exists for msg.sender
 address to check

____

<a id="events"></a>
##### Events

-----
<a id="getters"></a>
##### Getters

-----
<a id="setters"></a>
##### Setters

<a id="setterssetstorage"></a>
`setStorage`:
> Sets the contract address for the Storage Contract
>
> @param s Storage - The Storage object


<a id="settersadd"></a>
`add`:
>  Adds a custodian to the registry
>  Upon successful addition, the contract must emit `CustodianAdded(custodian)`
>  THROWS if the address has already been added, or is zero
>
>  @param custodian The address of the custodian


<a id="settersremove"></a>
`remove`:
>  Removes a custodian from the registry
>  Upon successful removal, the contract must emit `CustodianRemoved(custodian)`
>  THROWS if the address doesn't exist, or is zero
>
>  @param custodian The address of the custodian


<a id="setterssetfrozen"></a>
`setFrozen`:
>  Sets whether or not a custodian is frozen
>  Upon status change, the contract must emit `CustodianFrozen(custodian, frozen, owner)`
>
>  @param custodian The custodian address that is being updated
>  @param frozen Whether or not the custodian is frozen
