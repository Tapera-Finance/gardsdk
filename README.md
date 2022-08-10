# AlgoGard Python sdk package

This is the AlgoGard Python sdk package. If you have any issues, suggestions or questions, please reach out to us at hello@algogard.com

## Installation:

Run ```pip install gardsdk``` in your CLI/terminal.  

## Usage:

*note:* All methods require an Algorand client. Setup instructions can be found at https://developer.purestake.io/code-samples

\*Transaction has already been signed by logic

### cdp_creation.py contains the following methods:

#### open_cdp(address, client, total_malgs, GARD, account_id)

*Returns*  
If the user has opted into GARD: [ [PaymentTxn, OptInTxn\*], [ApplicationCallTxn, PaymentTxn, PaymentTxn, AssetTransferTxn\*] ]    
If the user has not yet opted into GARD: [ [PaymentTxn, OptInTxn\*, AssetTransferTxn], [ApplicationCallTxn, PaymentTxn, PaymentTxn, AssetTransferTxn*] ]

*Parameters*  
address (str) - Creator address  
client (AlgodClient) - Algorand client  
total_malgs (int) - Total amount of microAlgos being sent to the CDP as collateral  
GARD (int) - amount of microGARD being minted and sent to the creator address  
account_id (int) - An integer 7-127 associated with the CDP  

#### close_cdp_fee(usr_addr, client, account_id)  

*Returns*    
[ApplicationCallTxn\*, AssetTransferTxn, ApplicationClearTxn\*, PaymentTxn\*]

*Parameters*  
usr_addr (str) - The user's address  
client (AlgodClient) - Algorand client  
account_id (int) - An integer 7-127 associated with the CDP  

### cdp_actions.py contains the following methods:

#### add_collateral(client, addr, cdp_addr, amount)

*Returns*  
PaymentTxn

*Parameters*  
client (AlgodClient) - Algorand client  
addr (str) - Address sending collateral  
cdp_addr (str) - Address of CDP  
amount (int) - Amount of collateral being sent in microAlgo  

#### cdp_vote(usr_addr, client, account_id)

*Returns*  
[PaymentTxn, PaymentTxn\*]

*Parameters*  
usr_addr (str) - The user's address  
client (AlgodClient) - Algorand client  
account_id (int) - An integer 7-127 associated with the CDP  

#### mint_from_existing(address, client, account_id, to_mint)

*Returns*  
[ApplicationCallTxn\*, PaymentTxn, AssetTransferTxn\*]

*Parameters*  
address (str) - The user's address  
client (AlgodClient) - Algorand client  
account_id (int) - An integer 7-127 associated with the CDP  
to_mint (int) - amount of GARD to mint in microGARD  

#### liquidate(address, client, owner_address, account_id)  

*Returns*  
[ApplicationCallTxn\*, PaymentTxn\*, AssetTransferTxn, AssetTranferTxn, AssetTransferTxn]

*Parameters*  
address (str) - The user's address  
client (AlgodClient) - Algorand client  
owner_address (str) - The address of the CDP owner  
account_id (int) - An integer 7-127 associated with the CDP  

## Examples

### Opening a CDP
```python
from gardsdk import cdp_creation  
address = ""
key = [your secret key]
algod_client = [your algorand client]  
txns = cdp_creation.open_cdp(address, algod_client, 1*10**6, 4*10**6, 7) # Units are in microGARD and microAlgo (hence 10**6)  
txns[0][0] = txns[0][0].sign(key)
algod_client.send_transactions(txns[0])
for i in range(3): 
  txns[1][i] = txns[1][i].sign(key)
algod_client.send_transactions(txns[1])
```
### Adding collateral to a CDP

```python
from gardsdk import cdp_actions  
address = [your wallet address]
key = [your secret key]
algod_client = [your algorand client]    
cdp_address = [address of the CDP you are adding collateral to]  
txn = cdp_actions.add_collateral(algod_client, address, cdp_address, 1*10**6) # Units are in microGARD and microAlgo (hence 10**6) 
stxn = txn.sign(key)
algod_client.send_transaction(stxn)
```

