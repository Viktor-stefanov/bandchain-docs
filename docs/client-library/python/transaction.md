# Transaction Module

For sent the transaction on BandChain, ...

## Exceptions

| Type       | Description   |
| ---------- | ------------- |
| ValueError | Invalid value |

---

## with_messages(msgs)

Add one or multiple [`<Msg>`] to [`<Transaction>`].

#### Parameter

- **\*msgs** [`<Msg>`] Messages to be included with transaction

#### Return

[`<Transaction>`]

---

## with_auto(client)

[`<Transaction>`] must have at least 1 message already before using `withAuto()`. This function set `accountNumber` and `sequence` from [`<Client>`] with address from sender in `self.msgs[0]`.

#### Parameter

- **client** [`<Client>`] A client to set `accountNumber` and `sequence`

#### Return

[`<Transaction>`]

---

## with_account_num(account_num)

Set account number to [`<Transaction>`].

#### Parameter

- **account_num** `<int>`

#### Return

[`<Transaction>`]

---

## with_sequence(sequence)

Set sequence number to [`<Transaction>`].

#### Parameter

- **sequence** `<int>`

#### Return

[`<Transaction>`]

---

## with_chain_id(chain_id)

Set chain id to [`<Transaction>`].

#### Parameter

- **chain_id** `<str>`

#### Return

[`<Transaction>`]

---

## with_fee(fee)

Set fee to [`<Transaction>`].

#### Parameter

- **fee** `<int>`

#### Return

[`<Transaction>`]

---

## with_gas(gas)

Set gas to [`<Transaction>`].

#### Parameter

- **gas** `<int>`

#### Return

[`<Transaction>`]

---

## with_memo(memo)

Set memo to [`<Transaction>`].

#### Parameter

- **memo** `<str>` memo length is limited to 256.

#### Return

[`<Transaction>`]

---

## get_sign_data()

Get sign data from [`<Transaction>`].

#### Return

`<bytes>`

---

## get_tx_data(signature, pubkey)

Get transaction data from [`<Transaction>`].

#### Parameter

- **signature** `<bytes>`
- **pubkey** [`<PublicKey>`]

#### Return

`<dict>`

---

#### Example

```python
from pyband import Transaction
from pyband.message import MsgSend
from pyband.wallet import Address
from pyband.data import Coin

def main():
  to_adr = Address.from_acc_bech32("band1ksnd0f3xjclvg0d4z9w0v9ydyzhzfhuy47yx79")
  from_adr = Address.from_acc_bech32("band1jrhuqrymzt4mnvgw8cvy3s9zhx3jj0dq30qpte")
  coin = Coin(amount=100000, denom="uband")
  msg = MsgSend(to_address=to_adr, from_address=from_adr, amount=[coin])

  tsc = Transaction()
          .with_messages(msg)
          .with_account_num(100)
          .with_sequence(30)
          .with_chain_id("bandchain")
          .with_gas(500000)
          .with_fee(10)

  print(tsc.get_sign_data())

```

#### Result

```python
b'{"account_number":"100","chain_id":"bandchain","fee":{"amount":[{"amount":"10","denom":"uband"}],"gas":"500000"},"memo":"","msgs":[{"type":"oracle/Request","value":{"ask_count":"4","calldata":"AAAAA0JUQwAAAAAAAAAB","client_id":"from_pyband","min_count":"3","oracle_script_id":"1","sender":"band13eznuehmqzd3r84fkxu8wklxl22r2qfmtlth8c"}}],"sequence":"30"}'
```

---

#### Example

```python
from pyband import Transaction
from pyband.message import MsgRequest
from pyband.wallet import Address, PrivateKey
from pyband.data import Coin

def main():
  priv = PrivateKey.from_mnemonic("s")
  pubkey = priv.to_pubkey()
  addr = pubkey.to_address()

  tsc = (
      Transaction()
      .with_messages(
        MsgRequest(
          oracle_script_id=1,
          calldata=bytes.fromhex("000000034254430000000000000001"),
          ask_count=4,
          min_count=3,
          client_id="from_pyband",
          sender=addr,
        )
      )
      .with_account_num(100)
      .with_sequence(30)
      .with_chain_id("bandchain")
    )

  raw_data = tsc.get_sign_data()
  signature = priv.sign(raw_data)

  print(tsc.get_tx_data(signature, pubkey))

```

#### Result

```json
{
  "msg": [
    {
      "type": "oracle/Request",
      "value": {
        "oracle_script_id": "1",
        "calldata": "AAAAA0JUQwAAAAAAAAAB",
        "ask_count": "4",
        "min_count": "3",
        "client_id": "from_pyband",
        "sender": "band1jrhuqrymzt4mnvgw8cvy3s9zhx3jj0dq30qpte"
      }
    }
  ],
  "fee": { "gas": "200000", "amount": [{ "denom": "uband", "amount": "0" }] },
  "memo": "",
  "signatures": [
    {
      "signature": "aFvTgkY9F14dHfp2mtq8V2eCTKUtY1T9WKs99jmR8To3JB16cawbmpa1TRUdzfnqLXBh+o6XUuF4bHWR6xbCCw==",
      "pub_key": {
        "type": "tendermint/PubKeySecp256k1",
        "value": "A/5wi9pmUk/SxrzpBoLjhVWoUeA9Ku5PYpsF3pD1Htm8"
      },
      "account_number": "100",
      "sequence": "30"
    }
  ]
}
```

[`<transaction>`]: /client-library/python/transaction.html "Transaction"
[`<client>`]: /client-library/python/client.html "Client"
[`<msg>`]: /client-library/python/message.html "Message"
[`<publickey>`]: /client-library/python/wallet.html "PublicKey"