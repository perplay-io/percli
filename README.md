# percli

percli is a command line interface for HAVAH specialized for Perplay project.

## Installation

### Setup Environment

#### brew install

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
echo 'export PATH="$PATH:~/percli"' >> ~/.zshrc
source ~/.zshrc

brew install wget
brew install zip
#
```

#### dotnet runtime

- *note*: typeing root password required to install dotnet runtime

```
wget https://dot.net/v1/dotnet-install.sh
sudo chmod +x ./dotnet-install.sh
./dotnet-install.sh --version latest
echo 'export PATH="$PATH:$HOME/.dotnet"' >> ~/.zshrc
source ~/.zshrc
```

### percli

See `https://github.com/perplay-io/percli/tree/main/dist` for latest release.

Download the link of package zip for your platform.
ex) mac-x64: https://github.com/perplay-io/percli/raw/main/dist/percli_osx-x64/percli_osx-x64.zip

```
cd ~
mkdir percli
cd percli
wget https://github.com/perplay-io/percli/raw/main/dist/percli_osx-x64/percli_osx-x64.zip
unzip percli_osx-x64.zip
chmod +x percli
echo 'export PATH="$PATH:~/percli"' >> ~/.zshrc
source ~/.zshrc


percli
# if not working, try
dotnet ./percli.dll

:~/linux-x64$ ./percli
percli 0.1.0-tags-v0-0-7.1+222.88ecb44
Copyright (C) 2023 percli

ERROR(S):
  A required value not bound to option name is missing.

  -n, --network              (Default: vega) Network to use. (default: vega). [local, vega, mainnet]

  -q, --quiet                Suppresses summary messages.

  -y, --sayYes               answer to 'y'.

  -X, --xtimes               perform x times for some actions. [nwallet]

  --help                     Display this help screen.

  --version                  Display version information.

  Transaction Id (pos. 0)    Required. Target Transaction Id

```

If can't run binary, then you can alias with dotnet.

```
alias percli='dotnet ~/percli/percli.dll'
```

## Usage

### basic

#### import your wallet into `~/.wallets` path

You can get your 'private key hexstring' from Havah Wallet chrome/edge Extension.


Account tab -> Get Private Key -> Enter your password -> Copy the hexstring

ex) 8f3a0000000000009d83dbea200000000000000000b3f1a40000000000000450

It will produce `wallet private key json file` protected by AES-128 encryption with your new password provided.
 
- note: it's network-independent, just ignore it's 'vega' or 'mainnet'
 
```bash
cd ~
mkdir .wallets
cd .wallets

MY_PASSWORD=your_password
MY_PRIVATE_KEY=8f3a0000000000009d83dbea200000000000000000b3f1a40000000000000450

$ percli nwallet $MY_PASSWORD -k $MY_PRIVATE_KEY
<vega> https://ctz.vega.havah.io/api/v3
- KeyWallet file created: 22ddf2ff-f6cc-4ad9-aeff-68795b1aa8c2-hx2bcddd5c771ae3590b38026761b1e7cf36960aac.json
$ mv 22ddf2ff-f6cc-4ad9-aeff-68795b1aa8c2-hx2bcddd5c771ae3590b38026761b1e7cf36960aac.json mywallet-hx2bcddd5c771ae3590b38026761b1e7cf36960aac.json
```

#### displaying HVH PER balance
 
 
```
# percli hx8eff23fdff7736f05e4fef9e74f253d579025dcd
<vega> https://ctz.vega.havah.io/api/v3
hx8eff23fdff7736f05e4fef9e74f253d579025dcd: https://scan.vega.havah.io/address/hx8eff23fdff7736f05e4fef9e74f253d579025dcd
HVH: 9.763752400000000000
- PER: 99971782.002000000000000000
- PER (usable): 99971782.002000000000000000
- PER (naked): 99971782.002000000000000000
- PER (releasable): 0.000000000000000000
```

#### show balance with vesting intuitive info

```
# percli balanceOf hx5846991c8fab7925dd02ce162a69c98d8371dcf2 -V -n vega
<vega> https://ctz.vega.havah.io/api/v3
hx5846991c8fab7925dd02ce162a69c98d8371dcf2: https://scan.vega.havah.io/address/hx5846991c8fab7925dd02ce162a69c98d8371dcf2
HVH: 0.000000000000000000
- PER: 10.000000000000000000
- PER (usable): 0.000000000000000000
- PER (naked): 0.000000000000000000
- PER (releasable): 0.000000000000000000
=========== vesting info ===========
{
  "tokenGrant": {
    "Amount": 10000000000000000000,
    "Grantor": "hx8eff23fdff7736f05e4fef9e74f253d579025dcd",
    "IsActive": true,
    "ReleasedAmount": 0,
    "StartDay": "2023-10-01T00:00:00Z",
    "VestingLocation": "hx5846991c8fab7925dd02ce162a69c98d8371dcf2",
    "WasRevoked": false
  },
  "vestingSchedule": {
    "CliffDuration": 0,
    "Duration": 10627200,
    "Interval": 2125440,
    "IsRevocable": false,
    "IsValid": true,
    "ManualVestingSchedules": [
      "2023-10-01T00:00:00Z",
      "2023-11-01T00:00:00Z",
      "2023-12-01T00:00:00Z",
      "2024-01-01T00:00:00Z",
      "2024-02-01T00:00:00Z"
    ]
  }
}
```

### convert number, hex, datetime

```
percli 1696118400
percli 0x6681f180
# only utc times
percli 2024-07-01T00:00:00.000Z

percli 1000000000000000000
```

## transfer

### HVH

토큰을 명시하지 않으면 하바가 전송된다.

```
HXTEAM_KEY_JSON=/mnt/c/_repo/localley/bengal-smart-contracts/.wallets/vega/hxTEAM.json
HXTEAM_PASS=gochain # 미리 콘솔에 넣어두고 변수로 사용한다
alias percli=dotnet percli.dll

percli transfer \
    -n vega \
    hx5846991c8fab7925dd02ce162a69c98d8371dcf2 \
    1000000000000000000 \
    $HXTEAM_KEY_JSON $HXTEAM_PASS
<vega> https://ctz.vega.havah.io/api/v3
[transfer HVH] from: hx8eff23fdff7736f05e4fef9e74f253d579025dcd to: hx5846991c8fab7925dd02ce162a69c98d8371dcf2 amount: 1.000000000000000000
Are you sure? (y/n)
y
DONE: txid: 0x90db15abf4a29a09e42df58f01c852bf83f2a30cf370ee44ca56c859ec4c2cc4

percli hx5846991c8fab7925dd02ce162a69c98d8371dcf2
```

### PER

```
percli transfer \
    -n vega \
    -t PER \
    hx5846991c8fab7925dd02ce162a69c98d8371dcf2 \
    1000000000000000000 \
    $HXTEAM_KEY_JSON $HXTEAM_PASS
<vega> https://ctz.vega.havah.io/api/v3
[transfer PER] from: hx8eff23fdff7736f05e4fef9e74f253d579025dcd to: hx5846991c8fab7925dd02ce162a69c98d8371dcf2 amount: 1.000000000000000000
Are you sure? (y/n)
y
DONE: txid: 0x282d96857487b7ef186db94bf7697a7f1e569949f481cee9385d84d1d41e18d9

percli hx5846991c8fab7925dd02ce162a69c98d8371dcf2
```


## mint

VEGA_GENESIS_KEY_JSON=".wallets/vega/hx3cfb0ba9d84846f7ffca09329ab0a162ff9fc0e9-VEGA_PER_GENESIS.json"
VEGA_GENESIS_PASS=gochain # 미리 콘솔에 넣어두고 변수로 사용한다

MINTEE_ADDR=hx5846991c8fab7925dd02ce162a69c98d8371dcf2
PER_1=0xde0b6b3a7640000
PER_100=0x56bc75e2d63100000

```
# 숫자를 hex로 변환
percli 100000000000000000000
<vega> https://ctz.vega.havah.io/api/v3
helper: 100000000000000000000 -> 0x56bc75e2d63100000     <---- 이것을 사용하면 된다
tx hash is too short. 23

# 밸런스 체크
$ percli $MINTEE_ADDR
<vega> https://ctz.vega.havah.io/api/v3
KeyWallet Address: hx5846991c8fab7925dd02ce162a69c98d8371dcf2
HVH: 1.000000000000000000
- PER: 11.000000000000000000
- PER (usable): 1.000000000000000000
- PER (naked): 1.000000000000000000
- PER (releasable): 0.000000000000000000

#### 민팅
percli call mint \
    -n vega -c PER \
    "_beneficiary=$MINTEE_ADDR,_value=$PER_100" \
    $VEGA_GENESIS_KEY_JSON $VEGA_GENESIS_PASS
<vega> https://ctz.vega.havah.io/api/v3
[call cx55fe1cd2a2d36f2233d2cef077b1e06d2e7f233c] from: hx3cfb0ba9d84846f7ffca09329ab0a162ff9fc0e9 method:mint {"_beneficiary":"hx5846991c8fab7925dd02ce162a69c98d8371dcf2","_value":"100.000000000000000000"}
Are you sure? (y/n)
y
0x390337bb48656c8da0ef908cbc14ad3caf5bb47cdc10e5f2ac03d9ed20a49d2c

### 확인
$ percli $MINTEE_ADDR
<vega> https://ctz.vega.havah.io/api/v3
KeyWallet Address: hx5846991c8fab7925dd02ce162a69c98d8371dcf2
HVH: 1.000000000000000000
- PER: 111.000000000000000000
- PER (usable): 101.000000000000000000
- PER (naked): 101.000000000000000000
- PER (releasable): 0.000000000000000000
```

## vesting

```
HXTEAM_KEY_JSON=~/.wallets/hxTEAM.json
HXTEAM_PASS=gochain # 미리 콘솔에 넣어두고 변수로 사용한다
GRANTEE_ADDR=hx5846991c8fab7925dd02ce162a69c98d8371dcf2

percli vesting $GRANTEE_ADDR \
    -n vega \
    -t PER  \
    10000000000000000000 $HXTEAM_KEY_JSON $HXTEAM_PASS \
    2023-10-01T00:00:00.000Z \
    2023-11-01T00:00:00.000Z \
    2023-12-01T00:00:00.000Z \
    2024-01-01T00:00:00.000Z \
    2024-02-01T00:00:00.000Z
<vega> https://ctz.vega.havah.io/api/v3
[vesting PER] from: hx8eff23fdff7736f05e4fef9e74f253d579025dcd to: hx5846991c8fab7925dd02ce162a69c98d8371dcf2 amount: 10.000000000000000000
 STARTDAY [01]-[Utc] 2023-10-01 00:00:00, [Local] 2023-10-01 09:00:00 Released: 2.000000000000000000
          [02]-[Utc] 2023-11-01 00:00:00, [Local] 2023-11-01 09:00:00 Released: 4.000000000000000000
          [03]-[Utc] 2023-12-01 00:00:00, [Local] 2023-12-01 09:00:00 Released: 6.000000000000000000
          [04]-[Utc] 2024-01-01 00:00:00, [Local] 2024-01-01 09:00:00 Released: 8.000000000000000000
          [05]-[Utc] 2024-02-01 00:00:00, [Local] 2024-02-01 09:00:00 Released: 10.000000000000000000
Are you sure? (y/n)
y
{
  "_beneficiary": "hx5846991c8fab7925dd02ce162a69c98d8371dcf2",
  "_totalAmount": "0x8ac7230489e80000",
  "_vestingAmount": "0x8ac7230489e80000",
  "_startDay": "0x6518b680",
  "_duration": "0xa22880",
  "_cliffDuration": "0x0",
  "_interval": "0x206e80",
  "_isRevocable": "0x0",
  "_manualVestingSchedules": "1696118400:1698796800:1701388800:1704067200:1706745600"
}
0x633e89409869508a0b1716e6c6add32ece0e65c36359afba332875cee54d17f1

$ percli balanceOf $GRANTEE_ADDR -V -n vega
<vega> https://ctz.vega.havah.io/api/v3
hx5846991c8fab7925dd02ce162a69c98d8371dcf2: https://scan.vega.havah.io/address/hx5846991c8fab7925dd02ce162a69c98d8371dcf2
HVH: 1.000000000000000000
- PER: 111.000000000000000000
- PER (usable): 101.000000000000000000
- PER (naked): 101.000000000000000000
- PER (releasable): 0.000000000000000000
=========== vesting info ===========
{
  "tokenGrant": {
    "Amount": 10000000000000000000,
    "Grantor": "hx8eff23fdff7736f05e4fef9e74f253d579025dcd",
    "IsActive": true,
    "ReleasedAmount": 0,
    "StartDay": "2023-10-01T00:00:00Z",
    "VestingLocation": "hx5846991c8fab7925dd02ce162a69c98d8371dcf2",
    "WasRevoked": false
  },
  "vestingSchedule": {
    "CliffDuration": 0,
    "Duration": 10627200,
    "Interval": 2125440,
    "IsRevocable": false,
    "IsValid": true,
    "ManualVestingSchedules": [
      "2023-10-01T00:00:00Z",
      "2023-11-01T00:00:00Z",
      "2023-12-01T00:00:00Z",
      "2024-01-01T00:00:00Z",
      "2024-02-01T00:00:00Z"
    ]
  }
}
```


## 기타

## addGrantor

```
percli call -n vega -c PER-vesting-wallet addGrantor "_grantor=%TEAM_WALLET%" TPER_wallet.json gochain
```

## approve

```
percli call -y -n vega approve "_spender=%VEGA_PER_VESTING%,_value=0x14add2d593f6990ddc00000" 
```
