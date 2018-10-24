# T0ken Contract
The token contract which represents the security to be traded is compliant with [ERC-20][ERC-20] standard which
provides flexibility for the token to be used within other, and future, trading platforms.

The token contract is also compliant with [Delaware Senate Bill 69][bill-69] and [Title 8][title-8] of Delaware Code
relating to the General Corporation Law.

The token contract supports the Title 8's requirements:
- Token owners must have their identity verified (KYC).
- Must provide the following three functions of a “Corporations Stock Ledger” (Section 224 of the act).
- Enable the corporation to prepare the list of shareholders specified in Sections 219 and 220 of the act.
- Records “Partly Paid Share,” “Total Amount Paid,” “Total Amount to be Paid,” specified in Sections 156, 159, 217(a) and 218 of the act.
- Records transfers of shares, specified in Section 159 of the act.
- Requires that each token corresponds to a single share; i.e., no partial tokens
- Provides a way to re-issue tokens to a new address, and cancel the current one, in
the event

This document walk through the Solidity code for the token contract (`token.sol`) and uses Company A Preferred Stock
as an example company that likes to implement security tokens for offering equities (in this case, Company A Preferred Stock).

## Definition
The Token contract is the main contract that handles the base methods in the tZERO security token ecosystem.

![Token Design Diagram][design]

The rest of this page annotates the code components for the token contract ([`T0ken.sol`](../../contracts/token/T0ken.sol)).

* **[Libraries](#libraries)**
    * [Additive Math](#libadditivemath)
    * [Address Map](#libaddressmap)
* **[Variables](#variables)**
    * [Global Constants](#globalconstants)
    * [Global Variables](#globalvariables)
* **[Modifiers](#modifiers)**
    * [transferCheck](#modifiertransfercheck)
    * [isNotCancelled](#modifierisnotcancelled)
    * [onlyIssuer](#modifieronlyissuer)
    * [canIssue](#modifiercanissue)
    * [canTransfer](#modifiercantransfer)
    * [canTransferFrom](#modifiercantransferfrom)
* **[Events](#events)**
    * [VerifiedAddressSuperseded](#eventverifiedaddresssuperseded)
    * [IssuerSet](#eventissuerset)
    * [Issue](#eventissue)
    * [IssueFinished](#eventissuefinished)
* **[Functions](#functions)**
    * [transfer](#transer)
    * [transferFrom](#transferFrom)
    * [issueTokens](#issueTokens)
    * [finishIssuing](#finishIssuing)
    * [cancelAndReissue](#cancelAndReissue)
    * [approve](#approve)
* **[Getters](#getters)**
    * [name](#gettername)
    * [symbol](#gettersymbol)
    * [decimals](#getterdecimals)
    * [totalSupply](#gettertotalsupply)
    * [balanceOf](#getterbalanceof)
    * [allowance](#getterallowance)
    * [issuer](#getterissuer)
    * [holderAt](#getterholderat)
    * [isHolder](#getterisholder)
    * [isSuperseded](#getterissuperseded)
    * [getSuperseded](#gettergetsuperseded)
* **[Setters](#setters)**
    * [setCompliance](#settersetcompliance)
    * [setStorage](#settersetstorage)
    * [setIssuer](#settersetissuer)

---
<a id="libraries"></a>
#### Libraries
<a id="libadditivemath"></a>
[AdditiveMath](../../contracts/libs/math/AdditiveMath.sol)
> Provides overflow protected additive math functions for uint256

<a id="libaddressmap"></a>
[AddressMap](../../contracts/libs/collections/AddressMap.sol)
> Provides the address mapping structure

____
<a id="variables"></a>
### Variables
<a id="globalconstants"></a>
##### Global Constants
The set of constants that are referred to among the contracts that correspond to a token.

Parameter           | Type    | Visibility    | Value           | Description
------------------- | ------- | ------------- | --------------- | ----------------
`Company A Address`      | address | internal      | 0x0             |The zero address
`name`              | string  | public        | *(E.g. Company A Preferred)* | Type of equity . ERC-20 Required
`symbol`            | string  | public        | *(E.g. COMPA)* | Trading symbol. ERC-20 Required
`decimals`          | uint8   | public        | 0               | ERC-20 Required

____

<a id="globalvariables"></a>
##### Global Variables
The set of variables that are used among the contracts that correspond to a token.

Parameter           | Type       | Visibility    | Default | Description
------------------- | ---------- | ------------- | ------- | -----------------------
`shareholders`      | AddressMap | public        |     0x0    | The list of accounts that hold tokens.
`compliance`        | Compliance | public        |     -    |Instance of the Compliance rule set for the token
`store`             | Storage    | public        |     -    |Instance of the Storage contract for the token
`issuer`            | address    | public        |  -       | The address of the issuer account
`issuingFinished`   | bool       | public        | False   | Boolean value indicating if the token offering has ended.
`cancellations`     | mapping    | public        |   -      | `mapping(address => address)`: The mapping from a cancelled account address to its replacement account address.
`balances`          | mapping    | internal      |     -    | `mapping(address => uint256)`: The mapping from the token holder's address to the balance value.
`totalSupplyTokens` | uint256    | internal      |     -    | ERC-20 Required. The total sum of tokens in existenc.
`allowed`           | mapping    | private       |    - | `mapping (address => mapping (address => uint256))`: Used in determining if a token transfer is allowed.

____

<a id="modifiers"></a>
### Modifiers
<a id="modifiertransfercheck"></a>
`transferCheck`:
> Checks if the value is less than or equal to the balance of the from address.  
> * Otherwise, responds with "Balance is more than what from address has."

<a id="modifierisnotcancelled"></a>
`isNotCancelled`:
> Checks to make sure an address has not been canceled (in the case of a cancel and re-issue).  
> *  Otherwise, responds with "Address has been cancelled."

<a id="modifieronlyissuer"></a>
`onlyIssuer`:
> Checks to make sure `msg.sender` matches global variable `issuer`.  
> * Otherwise, responds with ""Only issuer is allowed."

<a id="modifiercanissue"></a>
`canIssue`:
> Checks if global variable `issuingFinished` is not True.
> * Otherwise, responds with "Issuing has already ended."

<a id="modifiercantransfer"></a>
`canTransfer`:  
> Checks if tokens can be transferred to `toAddress`.  
> * If `fromAddress` is `issuer`, checks if the `toAddress` exists.  
>   * If the `toAddress` does not exist, responds with "The to address does not exist."  
> * If `fromAddress` is not `issuer`, checks if the compliance rules allow the transfer from `fromAddress` to `toAddress`.
>   * If token cannot be transferred, responds with "The address cannot transfer."

<a id="modifiercantransferfrom"></a>
`canTransferFrom`:
> Checks if tokens can be transferred to `toAddress`.  
> * If `fromAddress` is `owner`, checks if the `toAddress` exists.  
>   * If the `toAddress` does not exist, responds with "The to address does not exist."  
> * If `fromAddress` is not `owner`, checks if the compliance rules allow the transfer from `fromAddress` to `toAddress`.
>   * If token cannot be transferred, responds with "The address cannot transfer."

____

<a id="events"></a>
### Events
<a id="eventverifiedaddresssuperseded"></a>
`VerifiedAddressSuperseded`:
> This event is emitted when a token address is cancelled and replaced with a new token address.  This happens in the case where a shareholder has lost access to their original token address and needs to have their token reissued to a new address.  This is the equivalent of issuing replacement share certificates.

<a id="eventissuerset"></a>
`IssuerSet`:
> This event is emitted when the issuer has been set, by the contract owner.

<a id="eventissue"></a>
`Issue`:
> This event is emitted when tokens are issued.

<a id="eventissuefinished"></a>
`IssueFinished`:
> This event is emitted when global variable `issuingFinished` has been set to True.

-----
<a id="functions"></a>
### Functions

<a id="transfer"></a>
`transfer`:
> Transfers specified number of tokens from `msg.sender` to the `toAddress`.  
>
> @param to address - The address to transfer to  
> @param uint256 value - The amount to be transferred  
> @return bool - A Boolean that indicates if the operation was successful

<a id="transferFrom"></a>
`transferFrom`:
> Transfers specified number of tokens from the `fromAddress` to the `toAddress`.
>
> @param from address - The address which you want to send tokens from  
> @param to address - The address which you want to transfer to  
> @param value uint256 - the amount of tokens to be transferred  
> @return bool - A Boolean that indicates if the operation was successful

<a id="#issueTokens"></a>
`issueTokens`:
> Tokens will be issued only to the issuer's address.
>
> @param quantity uint256 - The number of tokens to mint  
> @return bool - A Boolean that indicates if the operation was successful


<a id="#finishIssuing"></a>
`finishIssuing`:
> Sets issuing of tokens to "finished". No more tokens can be created after this method is called.
>
> @return bool - A Boolean that indicates if the operation was successful

<a id="#cancelAndReissue"></a>
`cancelAndReissue`:
> Cancels the original address and reissue the tokens to the replacement address.  
>
> *** Note: It is up to the issuer to make sure the replacement address belongs to a verified investor. ***  
>
> Access to this function MUST be strictly controlled.  
> The `original` address MUST be removed from the set of verified addresses.  
> Throw if the `original` address supplied is not a shareholder.  
> Throw if the replacement address is not a verified address.  
> This function MUST emit the `VerifiedAddressSuperseded` event.  
>
> @param original address - The address to be superseded. This address MUST NOT be reused.  
> @param replacement address - The address  that supersedes the original. This address MUST be verified.  

<a id="#approve"></a>
`approve`:
> Approves the passed address to spend the specified number of tokens on behalf of `msg.sender`.  
>
> Beware that changing an allowance with this method brings the risk that someone may use both the old and the new allowance by unfortunate transaction ordering. One possible solution to mitigate this race condition is to first reduce the spender's allowance to 0 and set the desired value afterwards: https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729

<span style="color:red">Have we implemented for this cautionary note?</span>  
> @param spender address - The address which will spend the funds  
> @param value  uint256 - The amount of tokens to be spent   
> @param bool - A Boolean that indicates if the operation was successful


-----
<a id="getters"></a>
### Getters

<a id="gettername"></a>
`name`:
> The name of the token  
>
> @return String - A string representing the name of the token (ERC-20)

<a id="gettersymbol"></a>
`symbol`:
> The symbol of the token
>
> @return String - A string representing the symbol of the token (ERC-20)

<a id="getterdecimals"></a>
`decimals`:
> The max number of decimal places a token may be divided into for trading
>
> @return uint8 - A uint8 representing the number of decimals places that may be individually transferred (ERC-20)

<a id="gettertotalsupply"></a>
`totalSupply`:
> The total supply of available Tokens
>
> @return uint256 - A uint256 representing the total supply of available tokens.

<a id="getterbalanceof"></a>
`balanceOf`:
> Returns the balance of a specific address
>
> @param addr address - The address to query the balance of  
> @return uint256 - A uint256 representing the amount owned by the passed address.  

<a id="getterallowance"></a>
`allowance`:
> Function to check the amount of tokens that an owner allowed to a spender.  
>
> @param addrOwner address - The address which owns the funds.  
> @param spender address - The address which will spend the funds.  
> @return uint256 - A uint256 specifying the amount of tokens still available for the spender.  

<a id="getterissuer"></a>
`issuer`:
> Returns issuer address.
>
> @return address - The address which is currently assigned as issuer

<a id="getterholderat"></a>
`holderAt`:
> By counting the number of token holders using `holderCount`  
> you can retrieve the complete list of token holders, one at a time.  
> It MUST throw if `index >= holderCount()`.  
>
> @param index uint256 - The zero-based index of the holder.  
> @return address - The address of the token holder with the given index.  

<a id="getterisholder"></a>
`isHolder`:
> Checks to see if the supplied address is a share holder.  
>
> @param addr address - The address to check.  
> @return bool - true if the supplied address currently owns a token.  

<a id="getterissuperseded"></a>
`isSuperseded`:
> Checks to see if the supplied address was superseded.  
>
> @param addr address - The address to check.  
> @return bool - true if the supplied address was superseded by another address.  

<a id="gettergetsuperseded"></a>
`getSuperseded`:
> Gets the most recent address, given a superseded one.  
> Addresses may be superseded multiple times, so this function needs to  
> be called until a ZERO_ADDRESS is returned (denoting not-superseded)
>
> @param addr address - The superseded address.  
> @return address - The verified address that supersedes the address parameter.  

-----
<a id="setters"></a>
### Setters

<a id="settersetcompliance"></a>
`setCompliance`:
> Sets the contract address for the Compliance contract
>
> @param newComplianceAddress address - The address of the compliance contract

<a id="settersetstorage"></a>
`setStorage`:
> Sets the contract address for the Storage contract
>
> @param s Storage - The Storage object

<a id="settersetissuer"></a>
`setIssuer`:
> Set issuer of token  
>
> @param newIssuer address - The address of the issuer  




[ERC-20]: //theethereum.wiki/w/index.php/ERC-20_Token_Standard
[bill-69]: //legis.delaware.gov/json/BillDetail/GenerateHtmlDocument?legislationId=25730&legislationTypeId=1&docTypeId=2&legislationName=SB69
[title-8]: //legis.delaware.gov/json/BillDetail/GenerateHtmlDocument?legislationId=25730&legislationTypeId=1&docTypeId=2&legislationName=SB69
[design]: http://www.plantuml.com/plantuml/png/dPNHQzim4CRVzLSOzh9H0cFFeJHqAoiK2adPQpXRPrT4bepkaX1A_lVPSkESQi-KwId-VQTFfxkhUaSCWVUrLGqKEwWmUp8vPSlb6Wi6LrcylStULDQkmW9Hzdnqa5liMLmtcJyw3CFtTflX0HrJ-sk0Rv0J1oZut3bWU0dWgCGGK7_zDaGjsdNrvVUOkE4zwgn4Ba--s8sICTHXrDEcXDpBKtu_aJZuqyXoPwoTEt-yxwpEwbAfvM8XdIKV79Iqa8BS6DbSW1gQ-E9tw4PTZAaPnnXAA33xjRx_yRt5dstSgcc2Fq0inLYzT5IeM-5pau6r_WYkm1Wnq6YQiXni5_TS9fGzWzticyQUKZuwMC27uEkfrlK5aaqkkYEj1JRUqS2_N9AveaL_4zGgcJhQO_W0ZAiUZ3FqYbeONk_XJp4Dabdukh6cEr10OXzGdkQWJlHJdNihWCR2r63fqwtzWy6K1lLAwu14RNOO23NgwLu_njjka6Va6UbIb7HKjjEzdf-CcSbwzLPZM7423VsEE8NombAeaF-X4FBzqAgFoePzW0h_Pqfd_Ye8kQzxcdk7-Z5AU1bh_DXFOLJIGLyvlKOpD6hyP4LFDNvcf1fV4F6jxj3fxf_9Nm00
