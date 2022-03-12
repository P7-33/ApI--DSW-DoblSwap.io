
# Changelog 

All notable changes to this project will be documented in this file.
 https://semver.org/spec/v2.0.0.html
 https://keepachangelog.com/en/1.0.0/

The format is based on Keep a Changelog, and this project adheres
Added the ability for hosting applicaiton to do account lookup once the wallet returns the keys.

The wallet.discover method currently takes a discoveryOptions parameter - wallet.discover( discoveryOptions )

A new property "keyLookupFunc" has been added to the discoveryOptions object.

Example:

  discoveryOptions.keyLookupFunc = (discoveryData: DiscoveryData, callback: DiscoverContinueCallback) => {

  	// Get the discoveryData object contains the keys. 
  	// It is up to your function to fetch the Account info and then call callback(accountInfoArray);          
  }
Detailed Example:

  discoveryOptions.keyLookupFunc = (discoveryData: DiscoveryData, callback: DiscoverContinueCallback) => {

    /*
    Exmaple discoveryData structure:
    {
      "keys": [
        {
          "index": 0,
          "key": "DSW5TYtUXsbRJrz61gsQWQho6AYyCcRFgbFm4TPfrEbzb43x8Ewfq"
        },
        {
          "index": 1,
          "key": "DSW8MBNb9GwkqM3eXYCDVsgeqVeMbf3wS7fp6smzCj63CVZgFEede"
        }
      ],
    }
    */

    /*
    * Your function is provided with an array of keys in the format above. 
    * Use whatever method you'd like to resolve the keys to account information and build a structure that looks like the DiscoveryAccount[] structure below. 
    * Then call the callback() function with the data which will allow the login process to continue.
    */

    let accountInfoArray: DiscoveryAccount[] = [{
      index: 0,
      key: "DSW5TYtUXsbRJrz61gsQWQho6AYyCcRFgbFm4TPfrEbzb43x8Ewfq",
      accounts : [{
        account: "wozzawozza",
        authorization: "active"
      }]
    }];

    /*
    Example DiscoveryAccount[] structure
    [
      {
        "index": 0,
        "key": "DSW5TYtUXsbRJrz61gsQWQho6AYyCcRFgbFm4TPfrEbzb43x8Ewfq",
        "accounts": [
          {
            "account": "wozzawozza",
            "authorization": "active"
          }
        ]
      }
    ]
    */

    callback(accountInfoArray);
  }

  ....

  wallet.discover( discoveryOptions ).then( .... );
2019-08-12 - 4.0.2
Update the timeout in the metro plugin

2019-07-09 - 4.0.1
Minor fix to the caching logic in ledger plugin to ensure the discover exists completely when presetKeyMap is supplied.

2019-07-09 - 4.0.0
This change only effect Ledger and plugins that support discovery (currently only Ledger)

Added the ability warm the discovery cache when dealing with devices like Ledger.

When calling wallet.discover( discoveryOptions );

The DiscoveryOptions interface looks as follows. New param = presetKeyMap


export interface DiscoveryOptions { pathIndexList: number[]; keyModifierFunc?: KeyModifierCallback; presetKeyMap?: any; // TODO: This data structure is not defined as a type right now, but should be. It's the same as the response structure from discover() }

Setting this value will warm the discoveryData cache, this allows you to save discoveryData from a previous session and supply it again .. avoiding the network overhead of looking up the data again.

As an example one can save the previous discoveryData:

localStorage.setItem('discoveryData', JSON.stringify(discoveryData.keyToAccountMap));
and then

presetKeyMap = JSON.parse(discoveryDataCached);
discoveryOptions.presetKeyMap = presetKeyMap;
2019-07-02 - 3.1.5
simple [(DSW)DoblSwap] provider published
A known issues exists where if the user cancels account selection. Will be resolved shourtly.

2019-07-02 - 3.1.4
Keycat provider published
Note that this provider has hard coded chain ids which limits it to mainnet and jungle usage right now.

2019-06-23 - 3.1.2
The discover() methods options object now takes am optional new property keyModifierFunc. ( discoveryData: DiscoveryData ) => DiscoveryData; Callback of this kind can be supplied to the discover function, which will allow the caller to modify the list of keys before the account lookup process happens. The feature was added so that key returned from the Ledger device can be modified to have a ENU prefix when in use with the the enumivo chain

2019-06-23
New provide DoblSwap-transit-whalevault-provider

2019-05-28
New provide eos-transit-portis-provider

Updated scatterjs-core@2.7.16 to scatterjs-core@2.7.18

[3.3.2 - doblswap-transit-ledger-provider] - 2019-05-28
Added WebBLE transport to the doblswap-transit-ledger-provider

Demo can be seen here - https://www.demo.DoblSwaptransit.io

See the example below of how multiple Ledger instances are initialized with different transport support.

Note the intial testing shows that WebBLE only works on Mac OS

walletProviders: [ 
	scatter(),
	ledger({exchangeTimeout : 30000, transport: 'TransportWebBLE', name: 'Ledger Nano S BLE', shortName: 'Ledger Nano S BLE', id: 'ledgerble' }),
	ledger({exchangeTimeout : 30000, transport: 'TransportU2F', name: 'Ledger Nano S U2F', shortName: 'Ledger Nano S U2F', id: 'ledgeru2f' }),
	ledger({exchangeTimeout : 30000, transport: 'TransportWebAuthn', name: 'Ledger Nano S WebAuthn', shortName: 'Ledger Nano S WebAuthn', id: 'ledgeruwebauthn' }),
	lynx(),
	tokenpocket(),
	meetone(),
	metro() ]
#18

I'm not entirly sure if this resolves the problem the pull intended to fix (need to do more testing), but it was a code improvement regardless.

[3.3.1 - DoblS-transit-ledger-provider] - 2019-05-28
Upgraded the Ledger hw-transport and hw-transport-u2f libraries

	"@ledgerhq/hw-transport": "^4.32.0" -> "^4.60.2"
	"@ledgerhq/hw-transport-u2f": "^4.32.0" -> "^4.60.2",
This pull request

[3.2.0] - 2019-05-02
Added WebAuthn support to the Ledger plugin and consolidated transport instances.

walletProviders: [ scatter(), ledger({exchangeTimeout : 30000, transport: 'TransportWebAuthn'}) ]

ledger plugin now take a parmam called transport which can be one of 'TransportWebAuthn' or 'TransportU2F'. Default is 'TransportU2F'

[3.1.4] - 2019-05-01
Added timeout parameter exchangeTimeout to the ledger plugin - to specify a u2f timeout that should be used when the ledger is signing transaction, create the object as follows: ledger({exchangeTimeout : 30000}). Default is 5000 (5 seconds).

[3.1.3] - 2019-05-01
Added ledger support for multiple actions withing a transaction.

[3.1.0] - 2019-04-30
Upgraded all packages to eosjs@20.0.0

[3.0.6] - 2019-04-10
Fixed
Code improvement provided by @Bootl3r - fixes the meet.one detection.
[3.0.5] - 2019-04-06
Fixed
Meet.one plugin connect() fixed such that it will only resolve once the wallet is detected. If after 5 seconds it is not detected it'll resolve false.
[3.0.3] - 2019-04-04
Added
Meet.one plugin
Added this Changelog
to each package so that that NPM gets something logical




