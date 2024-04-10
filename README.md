# Namada infrastructure & Validator instructions

## Infrastructures

#### Public RPC: 
```
https://rpc.se.morecon.online/
```

#### Indexer Service: 
```
https://indexer.se.morecon.online/block/last
```

#### Seed:
```
tcp://71aa78d811f4939c16dfc87c5e53265b7af59f2f@75.119.137.202:26657
```

#### Peer:
```
tcp://71aa78d811f4939c16dfc87c5e53265b7af59f2f@75.119.137.202:26657
```

#### Snapshot:
```
http://rpc.se.morecon.online:54321/se-snapshot.tar.gz
```

## Validator Setup

#### Recover key from registered account
```
namadaw derive --alias "${ALIAS}"
```

#### check key address
```
namadaw find --alias "${ALIAS}"
```

#### check balance well
```
namadac balance --owner "${ALIAS}"
```

#### find validator key
```
namadac find-validator --tm-address=$(curl -s localhost:26657/status | jq -r .result.validator_info.address)
```

#### initialize validator
```
namadac  init-validator \
 --alias "${VAL_NAME}" \
 --account-keys "${KEY_NAME}" \
 --signing-keys "${KEY_NAME}" \
 --commission-rate 0.1 \
 --max-commission-rate-change 0.1 \
 --email "${EMAIL}" \
```

#### bond tokens to your validator
```
namadac  bond \
 --source "${KEY_NAME}" \
 --validator "<Validator address>" \
 --amount 1000 \
```

## Validator management

#### validator consensus state
```
namadac validator-state --validator "<Validator address>"
```

#### unjail validator
```
namadac unjail-validator --validator  "<Validator address>"
```

#### claim rewards
```
namadac claim-rewards --validator "<Validator address>"
```

## Run node as Service
```
sudo tee /etc/systemd/system/namadad.service << EOF
[Unit]
Description=Namada Node
After=network.target
[Service]
User=$USER
WorkingDirectory=$HOME/.local/share/namada
Type=simple
ExecStart=/usr/local/bin/namada --base-dir=$HOME/.local/share/namada node ledger run
Environment=NAMADA_CMT_STDOUT=true
Environment=TM_LOG_LEVEL=p2p:none,pex:error
RemainAfterExit=no
Restart=on-failure
RestartSec=10s
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## Start servive
```
sudo systemctl daemon-reload
sudo systemctl enable namadad
sudo systemctl start namadad && sudo journalctl -u namadad -f -o --no-hostname cat
```
