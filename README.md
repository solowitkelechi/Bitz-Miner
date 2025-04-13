# Bitz-Miner
# Detailed guide to Bitz miner on Android
Step-by-Step Instructions to Install Linux (Ubuntu) on Android Using proot-distro and Install Solana CLI + solana-keygen (Agave)
> Prerequisites
- [Android Device: Android 7.0+ recommended (proot-distro has issues below 7). At least 4GB RAM and 10GB free storage for smooth compilation.] 
- [Termux: The terminal emulator hosting proot-distro. Download on Playstore] 
- [Internet: Stable Wi-Fi/mobile data for downloads.] 
- [Storage Access: Ensure Termux has storage permissions (Settings > Apps > Termux > Permissions > Storage).]

# Part 1: Install Ubuntu via proot-distro on Android 
> Step 1: Install Termux

1. Download Termux:
- Get from F-Droid (preferred, up-to-date): https://f-droid.org/packages/com.termux/
- Avoid Google Play version (outdated).
Install and open Termux.

2. Grant Permissions:
- Run:
```
termux-setup-storage
```
- Allow storage access when prompted.

3. Update Termux:
```
pkg update && pkg upgrade
```
> Step 2: Install proot-distro
1. Install proot-distro:
```
pkg install proot-distro
```
2. List Available Distros:
```
proot-distro list
```
- Look for ubuntu (e.g., ubuntu or ubuntu-24.04). Ubuntu is best for Solana due to package support.

> Step 3: Install Ubuntu
1. Install Ubuntu:
```
proot-distro install ubuntu
```
- Downloads a minimal Ubuntu rootfs (usually 22.04 or 24.04, ~200-500MB).
- If it fails (e.g., network error), retry or check storage:
2. Log In to Ubuntu:
```
proot-distro login ubuntu
```
- Prompt changes to root@localhost:~#, indicating you’re in Ubuntu.
- If login fails, try:
```
proot-distro login ubuntu --fix-low-ports
```

> Step 4: Set Up Ubuntu Environment
1. Update Packages:
```
apt-get update
apt-get upgrade -y
```
2. Install Basic Tools:
```
apt-get install -y curl git
```
3. Verify Architecture:
```
uname -m
```
- Expect: aarch64. Confirms your environment matches Solana’s build target.

# Part 2: Install Solana CLI and solana-keygen (Agave)
We’ll build solana and solana-keygen from Agave v2.1.18 source, as no aarch64 binaries exist.
> Step 5: Install Build Dependencies
Install packages needed for Rust and Agave:
```
apt-get install -y build-essential clang pkg-config libssl-dev zlib1g-dev protobuf-compiler libprotobuf-dev cmake
```
Verify:
```
pkg-config --modversion libssl
protoc --version
cmake --version
```
- Expect: 3.0.x (OpenSSL), 3.x.x (protoc), 3.x.x (cmake).
- If missing:
```
apt-get install --reinstall libssl-dev protobuf-compiler libprotobuf-dev cmake
```

> Step 6: Install Rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
- Choose default (option 1).
- Activate:
```
source $HOME/.cargo/env
```
- Update:
```
rustup update
```
- Verify:
```
rustc --version
```
Expect: rustc 1.80.1 or newer.

> Step 7: Clone Agave Source
```
git clone https://github.com/anza-xyz/agave.git
cd agave
git checkout v2.1.18
```
- Verify tag:
```
git status
```

> Step 8: Clean Build Environment
```
cargo clean
rm -rf target
```

> Step 9: Configure Environment
Set variables to avoid errors and optimize for Android:
```
export OPENSSL_NO_VENDOR=1
export OPENSSL_LIB_DIR=/usr/lib/aarch64-linux-gnu
export OPENSSL_INCLUDE_DIR=/usr/include
export PKG_CONFIG_PATH=/usr/lib/aarch64-linux-gnu/pkgconfig
export PROTOC=/usr/bin/protoc
export PROTOC_INCLUDE=/usr/include
export MAKEFLAGS="-j1"
```

Step 10: Build Solana CLI and solana-keygen
Build both binaries:
```
cargo build --release --bin solana
cargo build --release --bin solana-keygen
```
- Takes ~15-30 minutes total.
- If errors occur:
```
RUST_BACKTRACE=full cargo build --release --bin solana
```

> Step 11: Install Binaries
```
mkdir -p ~/bin
cp target/release/solana ~/bin/
cp target/release/solana-keygen ~/bin/
chmod +x ~/bin/solana ~/bin/solana-keygen
```

> Step 12: Update PATH
```
export PATH=$HOME/bin:$PATH
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Step 13: Verify Installation
```
solana --version
solana-keygen --version
```
- Expect: solana-cli 2.1.18 and solana-keygen 2.1.18.

> Step 14: Configure and Test
1. Set Devnet:
```
solana config set --url https://api.devnet.solana.com
solana config get
```
2. Create Wallet:
```
solana-keygen new
```
- Save seed phrase securely.
3. Test Airdrop:
```
solana airdrop 1
solana balance
```
- Expect: 1 SOL.

> Step 15: connect CLI to Eclipse mainnet
This connects CLI to the Eclipse mainnet.
```
solana config set --url https://mainnetbeta-rpc.eclipse.xyz/
```

> Step 15:Exporting PrivateKey from ID.json
```
cat ~/.config/solana/id.json
```
- Copy the output (a list of numbers) and import it into Backpack Wallet under “Private Key”.
- Fund the wallet with 0.005+ ETH on Eclipse to activate mining. 

# Part 3: Install Bitz CLI
This compiles and installs the Bitz miner directly from source.
```
cargo install bitz
```

Start Mining Bitz by opening Screen
```
screen -S bitzminer
```
Start Miner
```
bitz collect
```
Using Multiple core
```
bitz collect --cores 2
```
> To keep it running in the background:
- Detach screen: Ctrl + A + D
- Reattach screen: ```screen -r bitzminer``
- List screens: screen -ls
- Stop miner: Ctrl + C inside screen
- Kill screen: screen -XS bitzminer quit
I used 2 cores here (will adjsut appropriately according to my SPEC)

1. Claim Mined tokens:
```
bitz claim
```
2. Check Wallet status:
```
bitz account
```

# DONE ✅

 
