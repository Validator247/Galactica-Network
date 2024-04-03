# Galactica-Network

Step 1: Install Dependencies

Ensure your system is up to date and install necessary dependencies:

    sudo apt update && sudo apt upgrade -y
    sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y

Step 2: Set Environment Variables

Set up environment variables including your wallet, node name, chain ID, and port:

    echo "export WALLET="wallet"" >> $HOME/.bash_profile
    echo "export MONIKER="Your_nodename"" >> $HOME/.bash_profile
    echo "export GALACTICA_CHAIN_ID="galactica_9301-1"" >> $HOME/.bash_profile
    echo "export GALACTICA_PORT="46"" >> $HOME/.bash_profile
    source $HOME/.bash_profile

Step 3: Install Go

Download and install the Go programming language:

    cd $HOME
    VER="1.21.3"
    wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
    sudo rm -rf /usr/local/go
    sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
    rm "go$VER.linux-amd64.tar.gz"
    [ ! -f ~/.bash_profile ] && touch ~/.bash_profile
    echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
    source $HOME/.bash_profile
    [ ! -d ~/go/bin ] && mkdir -p ~/go/bin

Step 4: Download and Build Galactica

Clone the Galactica repository, checkout a specific version, build the binary, and move it to a directory in your PATH:

    cd $HOME
    rm -rf galactica
    git clone https://github.com/Galactica-corp/galactica
    cd galactica
    git checkout v0.1.1
    make build
    mv $HOME/galactica/build/galacticad $HOME/go/bin

Step 5: Configure and Initialize the App

Configure your Galactica node and initialize it:

    galacticad config node tcp://localhost:${GALACTICA_PORT}657
    galacticad config keyring-backend os
    galacticad config chain-id galactica_9301-1
    galacticad init "Your_nodename" --chain-id galactica_9301-1

Step 6: Download Genesis and Addrbook

Download genesis and addrbook files and place them in the appropriate directory:

    wget -O $HOME/.galactica/config/genesis.json https://testnet-files.itrocket.net/galactica/genesis.json
    wget -O $HOME/.galactica/config/addrbook.json https://testnet-files.itrocket.net/galactica/addrbook.json

Step 7: Set Seeds and Peers

Update the configuration file with seed nodes and persistent peers:

    SEEDS="52ccf467673f93561c9d5dd4434def32ef2cd7f3@galactica-testnet-seed.itrocket.net:46656"
    PEERS="c9993c738bec6a10cfb8bb30ac4e9ae6f8286a5b@galactica-testnet-peer.itrocket.net:11656,7cb717ce5cea0027ba4fef1bea5ff920809c9798@65.109.78.242:31380,017de01271dc2e5cda990860f27059cf3bf0149c@65.109.53.24:29656,4774752c35fd5fa4e0214ebda35054571756d756@65.109.92.240:34106,e926f2e20568e61646558a2b8fd4a4e46d77903f@141.95.110.124:26656,92ce8630d19e92f209058a35236cbd485b39c5b2@144.76.97.251:26756,875873e6bbd27b3e28ea6ef3ac5cabb406f0f554@51.159.16.102:24309,e1aec58daa2fb7167afadbf96924d7da153d67ea@5.189.184.209:26656,a028446e34e3c5bd198a60bf6e799a05e8db16a1@116.202.162.188:14656,028d8c875660f0e3fb1d893acd0b2220c619625f@157.90.158.222:26656,fe758700e25b59b6ba6e2784badcb6024ba1b760@168.119.241.1:26656"
    sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.galactica/config/config.toml

Step 8: Set Custom Ports

Update the configuration files with custom ports:

    sed -i.bak -e "s%:1317%:${GALACTICA_PORT}317%g;
    s%:8080%:${GALACTICA_PORT}080%g;
    s%:9090%:${GALACTICA_PORT}090%g;
    s%:9091%:${GALACTICA_PORT}091%g;
    s%:8545%:${GALACTICA_PORT}545%g;
    s%:8546%:${GALACTICA_PORT}546%g;
    s%:6065%:${GALACTICA_PORT}065%g" $HOME/.galactica/config/app.toml

Step 9: Configure Pruning, Minimum Gas Price, Prometheus, and Indexing

Set up various configurations in the app.toml and config.toml files:

    sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.galactica/config/app.toml
    sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.galactica/config/app.toml
    sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.galactica/config/app.toml

    sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "10agnet"|g' $HOME/.galactica/config/app.toml
    sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.galactica/config/config.toml
    sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.galactica/config/config.toml

Step 10: Create Systemd Service

Create a systemd service file for managing the Galactica node:

    galacticad tx staking create-validator \
    --amount 1000000000000000000agnet \
    --from wallet \
    --commission-rate 0.1 \
    --commission-max-rate 0.2 \
    --commission-max-change-rate 0.01 \
    --min-self-delegation 1 \
    --pubkey $(galacticad tendermint show-validator) \
    --moniker “Your-nodename” \
    --identity “keybase \
    --details “love you forever” \
    --website “yoủ-web” \
    --security-contact “your-mail” \
    --chain-id galactica_9301-1 \
    --gas-prices 10agnet \
    --gas 300000 \
    --yes







