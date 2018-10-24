<img src="https://www.tzero.com/img/Logo-Header6.png" width="400px" />

# The Trading Platform

A block-chain based trading platform serving as an Alternative Trading Solution (ATS) for the trading of securities.
The trading platform is an SEC compliant suite of smart contracts that allow trading and fast settlement of
securities in [T+0][T-plus-N] time. Built on the Ethereum chain, the platform provides secure, fault-tolerant, transparent, and
fast transaction settlement.

The platform provides methods for the issuance of [ERC-20][erc-20] compliant security tokens which can be customized
to represent various types of security.

In addition to the above, the token also satifies the amended [Title 8] of the Delaware Code Relating to the General
Corporation Law, which includes:

 - Token owners must have their identity verified (KYC).
 - Must provide the following three functions of a “Corporations Stock Ledger” (Section 224 of the act)
 - Enable the corporation to prepare the list of shareholders specified in Sections 219 and 220 of the act.
 - Records “Partly Paid Share,” “Total Amount Paid,” “Total Amount to be Paid,” specified in Sections 156, 159, 217(a)
   and 218 of the act.
 - Records transfers of shares, specified in Section 159 of the act.
 - Requires that each token corresponds to a single share; i.e., no partial tokens
 - Provides a way to re-issue tokens to a new address, and cancel the current one, in the event


This GitHub space describes the set of Ethereum contracts that represent tZERO's token and trading methods. Furthermore,
this GitHub space provides instructions and walk-throughs for third-party integration and customization.

## Components
For a token to exist and be tradable, it first has to be defined, created and then be constrained within the set of
regulatory rules to ensure compliance with the trading laws for the parties involved (investors, broker-dealers, and
custodians).

![Detailed Design Diagram][uml-overall]

The tokens are created and their trades validated within the following interrelated set of components:
 - [Token](docs/design/token.md)
 - [Registry](docs/design/registry.md)
 - [Compliance](docs/design/compliance.md)
 - [Support Library](/contracts/libs)

### T0ken
The token contract that defines and creates the tokens which are the securities to be traded.

*See the [Token contract page](docs/design/token.md) for in-depth details.*

### Registry
The registry is the grouping of storage, investor, broker-dealer, and custodian contracts that define and coordinate
the behavior of these interacting entities.

*See the [Registry page](docs/design/registry.md) for in-depth details.*

### Compliance
The Compliance is a contract that houses the set of trade rules and exemptions (e.g. [Reg A][reg-a], [Reg D][reg-d],
etc.). The compliance rule set allows valid trades and stops the improper trades from taking place.

*See the [Compliance page](docs/design/compliance.md) for in-depth details.*

### Support Library
The set of support contracts and functions that are used by the other contracts.

*See the [Library page](contracts/libs) to see the library files.*

## Third-Party Integration
The third-party integration processes enables businesses, broker-dealers, custodians, and investors to plan and
utilize tZERO's platform to raise funds for their operations or and trade securities with other interested parties.

*See the [Third-Party Integration page](docs/design/third-party-integration.md) for instructions.*


## License
This project is licensed under the [Apache 2.0][apache 2.0] license.

## Links
tZERO's website: www.tzero.com


[erc-20]: //theethereum.wiki/w/index.php/ERC20_Token_Standard
[T-plus-N]: //www.investopedia.com/terms/t/tplus1.asp
[Title 8]: //legis.delaware.gov/json/BillDetail/GenerateHtmlDocument?legislationId=25730&legislationTypeId=1&docTypeId=2&legislationName=SB69
[reg-a]: //www.sec.gov/smallbusiness/exemptofferings/rega
[reg-d]: //www.sec.gov/fast-answers/answers-regdhtm.html
[apache 2.0]: //www.apache.org/licenses/LICENSE-2.0.html
[uml-overall]: http://www.plantuml.com/plantuml/png/NL4zJyGm3DtzAwpRKo5cX4wq3AmzDiHWYAbEf0dA1z89yTzntOPUkzdlu-MvFQPCCPVHWQLCRvGOUnxEASSB_W3Yooc7I0E_JdDRKWxsJ5wtXnW-ENPCZgC2J_wRHI3BBomsl3C6_sqRzDg-8MeC87n46XaV-zRStinrdiNafmV01ylOXl7BIV8xptHV7AULFiWjnP64NL2fmonhcaOR2ztLuQIs9G6DkQ_e7kfsa8P1_MfwWhRSGcjJMCShyP6zbT_m1m00
