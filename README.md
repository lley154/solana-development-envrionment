# Solana Development Envrionment
## Setup Ubuntu Linux OS

#### Install Dependencies
- [Node.js](https://nodejs.org/en/download/)
- [Yarn](https://classic.yarnpkg.com/en/docs/install)
- [Rust](https://www.rust-lang.org/tools/install)
- [Solana CLI](https://github.com/AlmostEfficient/full-stack-solana-dev/blob/main/content/guides/getstarted/setup-local-development.md#3-install-solana-cli)
- [Anchor](https://www.anchor-lang.com/docs/installation)

#### Verify versions
```
$ solana --version; node -v; yarn --version; anchor --version
solana-cli 2.0.23 (src:a37bc6d8; feat:607245837, client:Agave)
v22.13.0
1.22.22
anchor-cli 0.29.0
```

#### Set Solana config url
```
$ solana config get
Config File: /home/lawrence/.config/solana/cli/config.yml
RPC URL: https://api.mainnet-beta.solana.com 
WebSocket URL: wss://api.mainnet-beta.solana.com/ (computed)
Keypair Path: /home/lawrence/.config/solana/id.json 
Commitment: confirmed 

$ solana config set --url localhost
$ solana config get
Config File: /home/lawrence/.config/solana/cli/config.yml
RPC URL: http://localhost:8899 
WebSocket URL: ws://localhost:8900/ (computed)
Keypair Path: /home/lawrence/.config/solana/id.json 
Commitment: confirmed 
```

#### Generate keypair used for Anchor
```
$ solana-keygen new
Generating a new keypair

For added security, enter a BIP39 passphrase

NOTE! This passphrase improves security of the recovery seed phrase NOT the
keypair file itself, which is stored as insecure plain text

BIP39 Passphrase (empty for none): 

Wrote new keypair to /home/lawrence/.config/solana/id.json
===============================================================================
pubkey: B4C6zZmVTdfKZREm6eFGykWGMb1fMM2B7VND7TAVkjUk
===============================================================================
Save this seed phrase and your BIP39 passphrase to recover your new keypair:
promote clean utility bonus virus primary desert road adult car possible unveil
```

#### Get Solana local account address
```
$ solana address
B4C6zZmVTdfKZREm6eFGykWGMb1fMM2B7VND7TAVkjUk
```

#### Start local validator
```
$ solana-test-validator
Ledger location: test-ledger
Log: test-ledger/validator.log
⠂ Initializing...                                                                                                Waiting for fees to stabilize 1...
Identity: 81Dk2HupaRc3fK2yKctKPCY2Mpgutib1zYWtyenm8dLD
Genesis Hash: A65eMEjVxebr36f7NtzMovTLtk2JCqWS18gFTzxpH657
Version: 2.0.23
Shred Version: 6918
Gossip Address: 127.0.0.1:1024
TPU Address: 127.0.0.1:1027
JSON RPC URL: http://127.0.0.1:8899
WebSocket PubSub URL: ws://127.0.0.1:8900
⠁ 00:00:23 | Processed Slot: 53 | Confirmed Slot: 53 | Finalized Slot: 22 | Full Snapshot Slot: - | Incremental Sna^C
```

In a new terminal window, start the logger
```
$ solana logs
```

#### Top up with additional funds
In a new terminal window
```
$ solana balance
500000000 SOL
$ solana airdrop 100
Requesting airdrop of 100 SOL

Signature: 4kkzMNDYjv8NKweRmgiSyFScTs369mVeUi2bT5CFPzE7Rh4TZ7kUG9UHkdJniUQHiMh1cT3kX57XGdJnQnGNQT2S

500000100 SOL
```

#### Compile and run the counter program
```
$ git clone https://github.com/lley154/full-stack-solana-dev.git
$ cd full-stack-solana-dev/counter
$ anchor keys sync
```

Set the correct directory path to your key file in the ```Anchor.toml``` file in the provider section
```
# Provider configuration
[provider]
cluster = "localnet"
wallet = "/your-home-directory-path/.config/solana/id.json"
```

Then start the test run
```
$ anchor test --skip-local-validator

Counter PDA: 4s4WZUgaYfocxk1kGzXj4cDfu1fx3H9x87j6XRzXo418
Transaction Signature: NYjNSXkpVpDS38Q44NEXVurkGiKwGWCoabDpsCMuw1SWoXi69CKvpFyBb48XLpSHDcSxVBPDDSmBgNezT2MMscS
Count: 0
    ✔ Is initialized! (239ms)
Transaction Signature: CYGDuPitEBarmZiLByjvTMVnoKjjJPHvS7gZegNWu3Zfo29xQcmLof5eND14F8t6C6M9caRhLEyQnHMYrqdVQBg
Count: 1
    ✔ Increment (411ms)


  2 passing (654ms)

Done in 2.22s.
```

#### Inspect the data account
```
$ solana account 4s4WZUgaYfocxk1kGzXj4cDfu1fx3H9x87j6XRzXo418

Public Key: 4s4WZUgaYfocxk1kGzXj4cDfu1fx3H9x87j6XRzXo418
Balance: 0.0010092 SOL
Owner: A5mKqPJntXNoGxXUpw47DiKQzA82SMrVM2JUwZ4mQByS
Executable: false
Rent Epoch: 18446744073709551615
Length: 17 (0x11) bytes
0000:   ff b0 04 f5  bc fd 7c 19  01 00 00 00  00 00 00 00   ......|.........
0010:   ff
```

#### Run the increment counter 3 more times
```
$ anchor run test-increment
$ anchor run test-increment
$ anchor run test-increment
```

#### Compare the final result and see the counter value is at 4 ( 1 + 8 byte field)
```
$ solana account 4s4WZUgaYfocxk1kGzXj4cDfu1fx3H9x87j6XRzXo418

Public Key: 4s4WZUgaYfocxk1kGzXj4cDfu1fx3H9x87j6XRzXo418
Balance: 0.0010092 SOL
Owner: A5mKqPJntXNoGxXUpw47DiKQzA82SMrVM2JUwZ4mQByS
Executable: false
Rent Epoch: 18446744073709551615
Length: 17 (0x11) bytes
0000:   ff b0 04 f5  bc fd 7c 19  04 00 00 00  00 00 00 00   ......|.........
0010:   ff  
```

#### Show all the programs owned by this account
```
$ solana program show --programs

Program Id                                   | Slot      | Authority                                    | Balance
4wTWJT3GsjKimkv3wPGwktJHDiu1DwLN6ro71aU54wRz | 40637     | B4C6zZmVTdfKZREm6eFGykWGMb1fMM2B7VND7TAVkjUk | 1.40350488 SOL
A5mKqPJntXNoGxXUpw47DiKQzA82SMrVM2JUwZ4mQByS | 45121     | B4C6zZmVTdfKZREm6eFGykWGMb1fMM2B7VND7TAVkjUk | 1.40350488 SOL
NFSpGsEHUEFsUgarFtvfFkWyRSkmQj37brf4H5T1oiU  | 19322     | B4C6zZmVTdfKZREm6eFGykWGMb1fMM2B7VND7TAVkjUk | 2.79550488 SOL

$ solana program show 4wTWJT3GsjKimkv3wPGwktJHDiu1DwLN6ro71aU54wRz

Program Id: 4wTWJT3GsjKimkv3wPGwktJHDiu1DwLN6ro71aU54wRz
Owner: BPFLoaderUpgradeab1e11111111111111111111111
ProgramData Address: 2m1WhuiVuZYH2gNUynA8s6RPDAgVBmgeGuynsL5nv23S
Authority: B4C6zZmVTdfKZREm6eFGykWGMb1fMM2B7VND7TAVkjUk
Last Deployed In Slot: 40637
Data Length: 201480 (0x31308) bytes
Balance: 1.40350488 SOL
```

#### To upgrade a program and calcuate rent costs
```
$ du -h target/deploy/counter.so 
200K	target/deploy/counter.so

$ solana rent 200000
Rent-exempt minimum: 1.39289088 SOL

$ solana program extend NFSpGsEHUEFsUgarFtvfFkWyRSkmQj37brf4H5T1oiU 200000

Extended Program Id NFSpGsEHUEFsUgarFtvfFkWyRSkmQj37brf4H5T1oiU by 200000 bytes
```

#### Close the program account(s)
```
$ solana program close A5mKqPJntXNoGxXUpw47DiKQzA82SMrVM2JUwZ4mQByS --bypass-warning 

Closed Program Id A5mKqPJntXNoGxXUpw47DiKQzA82SMrVM2JUwZ4mQByS, 1.40350488 SOL reclaimed
```











