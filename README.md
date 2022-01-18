## PyCardano


PyCardano is a standalone Cardano client written in Python. The library is able to create and sign transactions without 
depending on third-party Cardano serialization tools, such as
[cardano-cli](https://github.com/input-output-hk/cardano-node#cardano-cli) and 
[cardano-serialization-lib](https://github.com/Emurgo/cardano-serialization-lib), making it a light-weight library that 
is easy and fast to set up in all kinds of environments.

Current goal of this project is to enable developers to write off-chain and testing code only in Python for their DApps.
Nevertheless, we see the potential in expanding this project to a full Cardano node implementation in Python, which 
might be beneficial for faster R&D iterations.

### Installation


### Examples

#### Transaction creation and signing

```python
"""An example that demonstrates low-level construction of a transaction."""

from pycardano import (Address, AddressKey, PaymentKeyPair, PaymentSigningKey, Transaction, TransactionBody,
                       TransactionInput, TransactionId, TransactionOutput, TransactionWitnessSet,
                       VerificationKeyWitness)

# Define a transaction input
tx_id_hex = "732bfd67e66be8e8288349fcaaa2294973ef6271cc189a239bb431275401b8e5"
tx_in = TransactionInput(TransactionId(bytes.fromhex(tx_id_hex)), 0)

# Define an output address
addr = Address.decode("addr_test1vrm9x2zsux7va6w892g38tvchnzahvcd9tykqf3ygnmwtaqyfg52x")

# Define two transaction outputs, both to the same address, but with different amount.
output1 = TransactionOutput(addr, 100000000000)
output2 = TransactionOutput(addr, 799999834103)

# Create a transaction body from inputs and outputs
tx_body = TransactionBody(inputs=[tx_in],
                          outputs=[output1, output2],
                          fee=165897)

# Create signing key from a secret json file
sk = PaymentSigningKey.from_json("""{
        "type": "GenesisUTxOSigningKey_ed25519",
        "description": "Genesis Initial UTxO Signing Key",
        "cborHex": "5820093be5cd3987d0c9fd8854ef908f7746b69e2d73320db6dc0f780d81585b84c2"
    }""")

# Derive a verification key from the signing key
vk = AddressKey(PaymentKeyPair.from_private_key(sk.payload).verification_key.payload)

# Sign the transaction body hash
signature = sk.sign(tx_body.hash())

# Add verification key and the signature to the witness set
vk_witnesses = [VerificationKeyWitness(vk, signature)]

# Create final signed transaction
signed_tx = Transaction(tx_body, TransactionWitnessSet(vkey_witnesses=vk_witnesses))
```

See more usages under [examples](https://github.com/cffls/pycardano/tree/main/examples).

### Documentations



-----------------

### Development

#### Workspace setup

Clone the repository:

`git clone https://github.com/cffls/pycardano.git`

PyCardano uses [poetry](https://python-poetry.org/) to manage its dependencies. 
Install poetry for osx / linux / bashonwindows:

`curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -`

Go to [poetry installation](https://python-poetry.org/docs/#installation) for more details. 


Change directory into the repo, install all dependencies using poetry, and you are done!

`cd pycardano && poetry install`

#### Test

PyCardano uses [pytest](https://docs.pytest.org/en/6.2.x/) for unit testing.

When testing or running any program, it is recommended to enter 
a [poetry shell](https://python-poetry.org/docs/cli/#shell) in which all python dependencies are automatically 
configured: `poetry shell`.

Run all tests:
`pytest`

Run all tests including doctests:
`pytest --doctest-modules`

Run all tests in a specific test file:
`pytest test/pycardano/test_transaction.py`

Run a specific test function:
`pytest -k "test_transaction_body"`

Run a specific test function in a test file:
`pytest test/pycardano/test_transaction.py -k "test_transaction_body"`

#### Test coverage

Test coverage could be checked by running:
`pytest --cov=pycardano`

A coverage report visualized in html could be generated by running:
`pytest --cov=pycardano --cov-report html:cov_html`  

The generated report will be in folder `./cov_html`.

### Style guidelines

The package uses 
[Google style](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_google.html) docstring.

The code style could be checked by [flake8](https://flake8.pycqa.org/en/latest/): `flake8 pycardano`

### Docs generation

The majority of package documentation is created by the docstrings in python files. 
We use [sphinx](https://www.sphinx-doc.org/en/master/) with 
[Read the Docs theme](https://sphinx-rtd-theme.readthedocs.io/en/stable/) to generate the 
html pages.

Build htmls: 

`cd docs && make html`

Go to the main page: 

`open build/html/index.html` 


### Feature support

- [x] Shelly address           
- [x] Transaction builder      
- [x] Transaction signing      
- [x] Multi-asset              
- [ ] Native script            
- [ ] Plutus script                  
- [ ] Byron Address            
- [ ] Reward withdraw          
- [ ] HD Wallet                
- [ ] Staking certificates     
- [ ] Protocol proposal update 
