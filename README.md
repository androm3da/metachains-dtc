metachains
==========

Tool for accessing and dumping metachain data into the Florincoin blockchain.


#### Installation

You can clone this repository and use its module directly. Alternatively, you
can build a package and install it through pip:

    python setup.py sdist
    sudo pip install dist/metachains_dtc-0.1.4.tar.gz

#### Florincoin installation 
Instructions can be found at [INSTALL.md](/INSTALL.md).


#### Florincoin wrapper usage

This module contains a class that can be used to communicate via json-rpc.
It must be configured with the json rpc url, username and password:

    import metachains
    dtc = metachains.Florincoin(
        "http://127.0.0.1:11777",
        "florincoind",
        "secret-password")

Once you do this, you'll have access to the following methods:

    dtc.block_count()        # Returns the number of blocks in the chain.

    dtc.balance()            # Returns the wallet balance.

    dtc.address(account)     # Returns an address for the given account.

    dtc.blocks(index, n)     # Generates n blocks in the chain,
                             # starting from index.

    dtc.transactions(block)  # Generates all data transactions in a block,
                             # generating pairs (transaction_id, raw_data).

    dtc.send_data(raw_data)  # Sends data to the blockchain.

    dtc.send_data(raw_data, address, amount)  # Send data to the blockchain via a standard transaction.


#### Synchronizer usage

You can use `metachains.Synchronizer` to synchronize data between
a blockchain and a local database:

- `Synchronizer.scan_database` scans the database and uploads its data to the blockchain.
- `Synchronizer.scan_blockchain` scans the blockchain and uploads its data to the database.

The database object must respond to the following methods:

- `data_dump(n)` -- This should return a byte array with the data to be uploaded to the blockchain, up to n bytes. When there is nothing more to upload, it should return `None`.
- `data_load(data, txid)` -- Notifies the database of new content that has been added to the blockchain, in the given txid transaction.
- `last_known_block()` -- Returns the block height from where blockchain scanning should begin.


To use `Synchronizer`, you must first create a `Florincoin` and a database object:

    import metachains

    coin = metachains.Florincoin("http://host:port", "username", "password")
    database = ...

    sync = metachains.Synchronizer(coin, database)

    sync.scan_blockchain()  # Downloads new data *from* the blockchain.
    sync.scan_database()    # Uploads new data *to* the blockchain.
