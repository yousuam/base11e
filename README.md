# base11e
Building a Complete On-Chain Indexer for Base Using Python + SQLite
Base produces thousands of transactions every hour. A lightweight local indexer allows developers to:

track token transfers

monitor on-chain events

build dashboards

analyze smart contract activity

Python Example — simple indexer loop
from web3 import Web3
import sqlite3
import time

w3 = Web3(Web3.HTTPProvider("https://mainnet.base.org"))

db = sqlite3.connect("base_index.db")
cursor = db.cursor()

cursor.execute("""
CREATE TABLE IF NOT EXISTS blocks (
    number INTEGER PRIMARY KEY,
    hash TEXT,
    timestamp INTEGER
);
""")

last = w3.eth.block_number - 20

while True:
    block = w3.eth.get_block(last)
    cursor.execute("INSERT OR IGNORE INTO blocks VALUES (?, ?, ?)",
                   (block.number, block.hash.hex(), block.timestamp))
    db.commit()

    print(f"Indexed block {block.number}")
    last += 1
    time.sleep(0.1)
