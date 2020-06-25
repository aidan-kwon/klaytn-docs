# caver.kct.kip7 <a id="caver-klay-kip7"></a>

The `caver.kct.kip7` helps you easily handle a smart contract that implements KIP-7 as a JavaScript object on the Klaytn blockchain platform (Klaytn).

The `caver.kct.kip7` inherits [caver.contract](../caver.contract.md) to implement the KIP-7 token contract. The `caver.kct.kip7` holds the same properties of `caver.contract` whereas additional methods to implement extra features. This section only introduces the newly added bound methods of the `caver.kct.kip7`.

The abi and bytecode used in the caver.kct.kip7 were implemented using the example of [openzeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/tree/master/contracts/token/ERC20).

KIP-7에 관한 자세한 정보는 [Klaytn Improvement Proposals](https://kips.klaytn.com/KIPs/kip-7)를 참조하십시오.

## caver.kct.kip7.deploy <a id="caver-klay-kip7-deploy"></a>

```javascript
caver.kct.kip7.deploy(tokenInfo, deployer)
```
KIP-7 토큰 컨트랙트를 Klaytn 블록체인에 배포합니다. A contract deployed using caver.kct.kip7.deploy is a fungible token that follows the KIP-7 standard.

성공적으로 배포된 후, 프로미스는 새로운 KIP7 인스턴스를 반환할 것입니다.

**매개변수**

| 명칭        | 형식     | 설명                                                                                                           |
| --------- | ------ | ------------------------------------------------------------------------------------------------------------ |
| tokenInfo | object | Klaytn 블록체인에 KIP-7 토큰 컨트랙트를 배포하는 데 필요한 정보입니다. 자세한 내용은 아래 표를 참조하세요.                                           |
| deployer  | 문자열    | The address of the keyring to deploy the KIP-7 token contract. This keyring must have enough KLAY to deploy. |

The tokenInfo object must contain the following:

| 명칭            | 형식                                    | 설명                   |
| ------------- | ------------------------------------- | -------------------- |
| 명칭            | 문자열                                   | 토큰 이름입니다.            |
| 기호            | 문자열                                   | 토큰 심볼입니다.            |
| decimals      | number                                | 토큰이 사용하는 소수점 자릿수입니다. |
| initialSupply | BigNumber &#124; string &#124; number | 처음 공급될 토큰 총 수량입니다.   |

**NOTE** The `initialSupply` parameter accepts `number` type but if the fed value were out of the range capped by number.MAX_SAFE_INTEGER, it might cause an unexpected result or error. 이 경우, `BigNumber` 타입 값 사용이 권장되며, 특히 `uint256` 크기의 숫자 입력은 `BigNumber` 타입 값을 사용하는 것이 좋습니다.

**리턴값**

`PromiEvent`: 이벤트 이미터와 결합된 프로미스이며 새로운 KIP7 인스턴스를 반환합니다. 추가로 다음 이벤트가 발생할 수 있습니다.

| 명칭              | 형식     | 설명                                                                                                                                                                                           |
| --------------- | ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| transactionHash | 문자열    | 트랜잭션이 전송된 직후 및 트랜잭션 해시를 사용할 수 있을 때 발생합니다.                                                                                                                                                    |
| receipt         | object | 트랜잭션 영수증을 사용할 수 있을 때 발생합니다. If you want to know about the properties inside the receipt object, see [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다. |
| error           | 에러     | 전송 중 오류가 나타나면 발생됩니다.                                                                                                                                                                         |

**예시**

```javascript
// using the promise
> caver.kct.kip7.deploy({
    name: 'Jasmine',
    symbol: 'JAS',
    decimals: 18,
    initialSupply: '100000000000000000000',
}, '0x{address in hex}').then(console.log)
KIP7 {
    ...
    _address: '0x598367e443D8a2b644Fec69a2C12aF44BC283f23',
    _jsonInterface: [
        ...
        {
            anonymous: false,
            inputs: [
                { indexed: true, name: 'owner', type: 'address' },
                    { indexed: true, name: 'spender', type: 'address' },
                    { indexed: false, name: 'value', type: 'uint256' }
            ],
            name: 'Approval',
            type: 'event',
            signature:  '0x8c5be...'
        }
    ] 
}

// using event emitter and promise
> caver.kct.kip7.deploy({
    name: 'Jasmine',
    symbol: 'JAS',
    decimals: 18,
    initialSupply: '100000',
}, '0x{address in hex}')
.on('error', function(error) { ... })
.on('transactionHash', function(transactionHash) { ... })
.on('receipt', function(receipt) {
    console.log(receipt.contractAddress) // contains the new token contract address
})
.then(function(newKIP7Instance) {
    console.log(newKIP7Instance.options.address) // instance with the new token contract address
})
```


## new KIP7 <a id="new-kip7"></a>

```javascript
new caver.kct.kip7([tokenAddress])
```
새로운 KIP7 인스턴스를 인스턴스 메소드, 이벤트들과 함께 생성합니다.

**매개변수**

| 명칭           | 형식  | 설명                                                                                                                          |
| ------------ | --- | --------------------------------------------------------------------------------------------------------------------------- |
| tokenAddress | 문자열 | (optional) The address of the KIP-7 token contract, which can be assigned later through `kip7.options.address = '0x1234..'` |


**리턴값**

| 형식     | 설명                                  |
| ------ | ----------------------------------- |
| object | 인스턴스 메소드와 이벤트들을 갖고 있는 KIP7 인스턴스입니다. |


**예시**

```javascript
// Create a KIP7 instance without a parameter
> const kip7 = new caver.kct.kip7()

// Create a KIP7 instance with a token address
> const kip7 = new caver.kct.kip7('0x{address in hex}')
```


## kip7.clone <a id="kip7-clone"></a>

```javascript
kip7.clone([tokenAddress])
```
현재 KIP7 인스턴스를 복제합니다.

**매개변수**

| 명칭           | 형식  | 설명                                                                                |
| ------------ | --- | --------------------------------------------------------------------------------- |
| tokenAddress | 문자열 | (선택 사항) 다른 KIP7 토큰을 배포했던 스마트 컨트랙트 주소입니다. 입력을 생략하면, 이 주소는 원본 인스턴스의 컨트랙트 주소로 설정됩니다. |

**리턴값**

| 형식     | 설명                         |
| ------ | -------------------------- |
| object | 원본 KIP7 인스턴스를 복제한 인스턴스입니다. |


**예시**

```javascript
> const kip7 = new caver.kct.kip7(address)

// Clone without a parameter
> const cloned = kip7.clone()

// Clone with the address of the new token contract
> const cloned = kip7.clone('0x{address in hex}')
```

## kip7.supportsInterface <a id="kip7-supportsinterface"></a>

```javascript
kip7.supportsInterface(interfaceId)
```
Return `true` if this contract implements the interface defined by `interfaceId`.

**매개변수**

| 명칭          | 형식  | 설명                  |
| ----------- | --- | ------------------- |
| interfaceId | 문자열 | 확인할 interfaceId입니다. |

**리턴값**

`프로미스`는 `Boolean`을 반환: 이 컨트랙트가 해당 `interfaceId`를 가진 인터페이스를 구현한다면 `true`를 반환합니다.

**예시**

```javascript
> kip7.supportsInterface('0x65787371').then(console.log)
true

> kip7.supportsInterface('0x3a2820fe').then(console.log)
false
```


## kip7.name <a id="kip7-name"></a>

```javascript
kip7.name()
```
Return the name of the token.

**매개변수**

없음

**리턴값**

`Promise` returns `string`: The name of the token.

**예시**

```javascript
> kip7.name().then(console.log)
Jasmine
```


## kip7.symbol <a id="kip7-symbol"></a>

```javascript
kip7.symbol()
```
Return the symbol of the token.

**매개변수**

없음

**리턴값**

`Promise` returns `string`: The symbol of the token.

**예시**

```javascript
> kip7.symbol().then(console.log)
JAS
```


## kip7.decimals <a id="kip7-decimals"></a>

```javascript
kip7.decimals()
```
Return the number of decimal places the token uses.

**매개변수**

없음

**리턴값**

`Promise` returns `number`: The number of decimal places the token uses.

**예시**

```javascript
> kip7.decimals().then(console.log)
18
```


## kip7.totalSupply <a id="kip7-totalsupply"></a>

```javascript
kip7.totalSupply()
```
Return the total token supply.

**매개변수**

없음

**리턴값**

`Promise` returns `BigNumber`: The total number of tokens.

**예시**

```javascript
> kip7.totalSupply().then(console.log)
100000000000000000000
```


## kip7.balanceOf <a id="kip7-balanceof"></a>

```javascript
kip7.balanceOf(address)
```
Return the balance of the given account address.

**매개변수**

| 명칭      | 형식  | 설명                |
| ------- | --- | ----------------- |
| address | 문자열 | 잔액을 확인할 계정 주소입니다. |

**리턴값**

`Promise` returns `BigNumber`: The account balance.

**예시**

```javascript
> kip7.balanceOf('0x{address in hex}').then(console.log)
100000
```


## kip7.allowance <a id="kip7-allowance"></a>

```javascript
kip7.allowance(owner, spender)
```
Return the amount of token that `spender` is allowed to withdraw from `owner`.

**매개변수**

| 명칭      | 형식  | 설명                             |
| ------- | --- | ------------------------------ |
| owner   | 문자열 | 토큰 소유자 계정의 주소입니다.              |
| spender | 문자열 | 토큰 소유자를 대신해 토큰을 사용하는 계정 주소입니다. |

**리턴값**

`프로미스`는 `BigNumber`를 반환: 토큰 소유자를 대신해 토큰 사용자가 사용할 수 있도록 남아있는 토큰 수량입니다.

**예시**

```javascript
> kip7.allowance('0x{address in hex}', '0x{address in hex}').then(console.log)
0

> kip7.allowance('0x{address in hex}', '0x{address in hex}').then(console.log)
10
```


## kip7.isMinter <a id="kip7-isminter"></a>

```javascript
kip7.isMinter(address)
```
Return `true` if the given account is a minter who can issue new KIP7 tokens.

**매개변수**

| 명칭      | 형식  | 설명                             |
| ------- | --- | ------------------------------ |
| address | 문자열 | 발행 권한을 가지고 있는지를 확인받을 계정 주소입니다. |

**리턴값**

`Promise` returns `Boolean`: `true` if the account is a minter.

**예시**

```javascript
> kip7.isMinter('0x{address in hex}').then(console.log)
true

> kip7.isMinter('0x{address in hex}').then(console.log)
false
```


## kip7.isPauser <a id="kip7-ispauser"></a>

```javascript
kip7.isPauser(address)
```
Return `true` if the given account is a pauser who can suspend transferring tokens.

**매개변수**

| 명칭      | 형식  | 설명                                |
| ------- | --- | --------------------------------- |
| address | 문자열 | 토큰 전송을 멈출 권한이 있는지를 확인받을 계정 주소입니다. |

**리턴값**

`Promise` returns `Boolean`: `true` if the account is a pauser.

**예시**

```javascript
> kip7.isPauser('0x{address in hex}').then(console.log)
true

> kip7.isPauser('0x{address in hex}').then(console.log)
false
```


## kip7.paused <a id="kip7-paused"></a>

```javascript
kip7.paused()
```
Return `true` if the contract is paused, and `false` otherwise.

**매개변수**

없음

**리턴값**

`Promise` returns `Boolean`: `true` if the contract is paused.

**예시**

```javascript
> kip7.paused().then(console.log)
true

> kip7.paused().then(console.log)
false
```


## kip7.approve <a id="kip7-approve"></a>

```javascript
kip7.approve(spender, amount [, sendParam])
```
토큰 소유자의 토큰을 `spender`가 `amount`만큼 사용하도록 허락합니다.

이 메서드는 토큰 소유자가 트랜잭션 발신자로서 Klaytn 네트워크에 트랜잭션을 전송하며, 트랜잭션 수수료가 트랜잭션 발신자에게 부과됩니다.

**매개변수**

| 명칭        | 형식                                    | 설명                                           |
| --------- | ------------------------------------- | -------------------------------------------- |
| spender   | 문자열                                   | 토큰 소유자를 대신해 토큰을 사용하는 계정 주소입니다.               |
| amount    | BigNumber &#124; string &#124; number | 토큰 사용자가 사용을 허락받은 토큰 수량입니다.                   |
| sendParam | object                                | (선택 사항) 트랜잭션을 보내는 데 필요한 파라미터들을 가지고 있는 객체입니다. |

**NOTE** The `amount` parameter accepts `number` type but if the fed value were out of the range capped by number.MAX_SAFE_INTEGER, it might cause an unexpected result or error. 이 경우, `BigNumber` 타입 값 사용이 권장되며, 특히 `uint256` 크기의 숫자 입력은 `BigNumber` 타입 값을 사용하는 것이 좋습니다.

`sendParam` 객체는 다음을 포함합니다:

| 명칭       | 형식                                              | 설명                                                                                                                                                                                      |
| -------- | ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| from     | 문자열                                             | (optional) The address from which the transaction should be sent. If omitted, it will be set by `this.options.from`. `sendParam`객체의 `from` 또는 `this.options.from`가 주어지지 않으면, 에러가 발생합니다. |
| gas      | number &#124; string                            | (선택 사항) 이 트랜잭션이 쓸 수 있는 최대 가스량 (가스 제한) 입니다. 생략되면, caver-js가 `this.methods.approve(spender, amount).estimateGas({from})`를 호출하여 이 값을 지정합니다.                                                |
| gasPrice | number &#124; string                            | (선택 사항) 이 트랜잭션에 사용할 peb 단위의 가스 가격. 생략하면 `caver.klay.getGasPrice` 값으로 caver-js가 설정합니다.                                                                                                   |
| value    | number &#124; string &#124; BN &#124; BigNumber | (선택 사항) peb으로 환산한 전송될 토큰 가치.                                                                                                                                                            |

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given 
> kip7.approve('0x{address in hex}', 10, { from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0xf010a98f66b6b36943175cd5b249da54e84abed551cfa02846a2900ddab968c7',
    blocknumber: 2098,
    contractAddress: null,
    from: '0x2f7dc98bd93a0544b03d6ff428a6f4ae04b32676',
    ...
    status: true,
    to: '0x8ca777e464a83b939ae131ca037f0d8728c6929e',
    ...
    events: {
        Approval: {
            address: '0x8CA777e464a83b939AE131CA037F0d8728C6929e',
            blocknumber: 2098,
            transactionHash: '0xf7469c0420cb5ebb0fbf64a314bd0c9ee7517ea64dd72eefa59bc8005bbc0f99',
            transactionIndex: 0,
            blockHash: '0xf010a98f66b6b36943175cd5b249da54e84abed551cfa02846a2900ddab968c7',
            logIndex: 0,
            id: 'log_c6ec61aa',
            returnValues: {
                '0': '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                '1': '0xe36ffD7bc4D588c480B5925B9622881F9d85ea30',
                '2': '10',
                owner: '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                spender: '0xe36ffD7bc4D588c480B5925B9622881F9d85ea30',
                value: '10'
            },
            event: 'Approval',
            signature: '0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925',
            raw: {
                data: '0x000000000000000000000000000000000000000000000000000000000000000a',
                topics: [ '0x8c5be...', '0x00...676', '0x00...a30' ]
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.approve('0x{address in hex}', 10).then(console.log)
```


## kip7.transfer <a id="kip7-transfer"></a>

```javascript
kip7.transfer(recipient, amount [, sendParam])
```
주어진 `amount`만큼의 토큰을 토큰 소유자 잔액에서 `recipient`에게 보냅니다. 토큰 소유자는 이 토큰 전송을 직접 실행해야 합니다. Thus, the token owner should be the sender of this transaction whose address must be given at `sendParam.from` or `kip7.options.from`. Without `sendParam.from` nor `kip7.options.from` being provided, an error would occur.

트랜잭션 전송은 트랜잭션 발신자에게 트랜잭션 수수료를 부과함을 유의하십시오.

**매개변수**

| 명칭        | 형식                                    | 설명                                                                                                                                                                            |
| --------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| recipient | 문자열                                   | 토큰을 받을 계정 주소입니다.                                                                                                                                                              |
| amount    | BigNumber &#124; string &#124; number | 전송될 토큰 수량입니다.                                                                                                                                                                 |
| sendParam | object                                | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** The `amount` parameter accepts `number` type but if the fed value were out of the range capped by number.MAX_SAFE_INTEGER, it might cause an unexpected result or error. 이 경우, `BigNumber` 타입 값 사용이 권장되며, 특히 `uint256` 크기의 숫자 입력은 `BigNumber` 타입 값을 사용하는 것이 좋습니다.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given 
> kip7.transfer('0x{address in hex}', 10, { from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0x8a078c3a73d678cdd85d471eb21e9ed7d695f8b96fc7315cfa59c1f68be3d2bf',
    blocknumber: 1353,
    contractAddress: null,
    from: '0x2f7dc98bd93a0544b03d6ff428a6f4ae04b32676',
    ...
    status: true,
    to: '0x05871c21664e18b2906545f8831695650a8f4056',
    ...
    events: {
        Transfer: {
            address: '0x05871c21664E18b2906545f8831695650a8f4056',
            blocknumber: 1353,
            transactionHash: '0x8bd2b21a06241e4cfc0af1ec40e7b15444f730c7529440648aa4ed6b697f08f4',
            transactionIndex: 0,
            blockHash: '0x8a078c3a73d678cdd85d471eb21e9ed7d695f8b96fc7315cfa59c1f68be3d2bf',
            logIndex: 0,
            id: 'log_82ef7705',
            returnValues: {
                '0': '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                '1': '0xE411cb0B61FDcC06497794fE3f49F65D5dE41f59',
                '2': '10',
                from: '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                to: '0xE411cb0B61FDcC06497794fE3f49F65D5dE41f59',
                value: '10'
            },
            event: 'Transfer',
            signature: '0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef',
            raw: {
                data: '0x000000000000000000000000000000000000000000000000000000000000000a',
                topics: [ '0xddf25...', '0x00...676', '0x00...f59' ]
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.transfer('0x{address in hex}', 10).then(console.log)
```

## kip7.safeTransfer <a id="kip7-safetransfer"></a>

```javascript
kip7.safeTransfer(recipient, amount [, data] [, sendParam])
```
주어진 `amount`만큼의 토큰을 토큰 소유자 잔액에서 `recipient`에게 안전하게 보냅니다. 토큰 소유자는 이 토큰 전송을 직접 실행해야 합니다. Thus, the token owner should be the sender of this transaction whose address must be given at `sendParam.from` or `kip7.options.from`. Without `sendParam.from` nor `kip7.options.from` being provided, an error would occur.

만약 토큰 수신자 주소가 컨트랙트 주소라면, 컨트랙트는 반드시 [IKIP7Receiver.onKIP7Received](https://kips.klaytn.com/KIPs/kip-7#wallet-interface)를 구현했어야 합니다. 그렇지 않으면, 전송은 거부됩니다.

트랜잭션 전송은 트랜잭션 발신자에게 트랜잭션 수수료를 부과함을 유의하십시오.

**매개변수**

| 명칭        | 형식                                    | 설명                                                                                                                                                                            |
| --------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| recipient | 문자열                                   | The address of the account to receive the token.                                                                                                                              |
| amount    | BigNumber &#124; string &#124; number | 전송할 토큰 수량입니다.                                                                                                                                                                 |
| data      | Buffer &#124; string &#124; number    | (optional) The optional data to send along with the call.                                                                                                                     |
| sendParam | object                                | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** The `amount` parameter accepts `number` type but if the fed value were out of the range capped by number.MAX_SAFE_INTEGER, it might cause an unexpected result or error. 이 경우, `BigNumber` 타입 값 사용이 권장되며, 특히 `uint256` 크기의 숫자 입력은 `BigNumber` 타입 값을 사용하는 것이 좋습니다.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given (without data)
> kip7.safeTransfer('0x{address in hex}', 10, { from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0x208cd64b95bbd91420fc6b1a7b514a8d3051d09333d79244b6b74ff2f7f3eee4',
    blocknumber: 2384,
    contractAddress: null,
    from: '0xc2c84328845a36fe0c4dcef370d24ec80cf85221',
    ...
    status: true,
    to: '0xe4aeba6306b0df023aa4b765960fa59dbe925950',
    ...
    events: {
            Transfer: {
                    address: '0xe4AeBa6306b0Df023AA4b765960fA59dbE925950',
                    blocknumber: 2384,
                    transactionHash: '0x47bb085947c282722c1ceab1f4f0380d911ce464a47a19f1e7bddfe08a13563d',
                    transactionIndex: 0,
                    blockHash: '0x208cd64b95bbd91420fc6b1a7b514a8d3051d09333d79244b6b74ff2f7f3eee4',
                    logIndex: 0,
                    id: 'log_58e5e06d',
                    returnValues: {
                            '0': '0xC2C84328845A36Fe0c4DcEf370d24ec80cF85221',
                            '1': '0x67B092d09B5e94fed58609777cc7Ac9193553B73',
                            '2': '10',
                            from: '0xC2C84328845A36Fe0c4DcEf370d24ec80cF85221',
                            to: '0x67B092d09B5e94fed58609777cc7Ac9193553B73',
                            value: '10',
                    },
                    event: 'Transfer',
                    signature: '0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef',
                    raw: {
                            data: '0x000000000000000000000000000000000000000000000000000000000000000a',
                            topics: [ '0xddf25...', '0x00...221', '0x00...b73' ],
                    },
            },
    },
}

// Send via a sendParam object with the from field given (with data)
> kip7.safeTransfer('0x{address in hex}', 11, '0x1234', { from: '0x{address in hex}' }).then(console.log)

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.safeTransfer('0x{address in hex}', 11).then(console.log)
```


## kip7.transferFrom <a id="kip7-transferfrom"></a>

```javascript
kip7.transferFrom(sender, recipient, amount [, sendParam])
```
주어진 `amount`만큼의 토큰을 토큰 소유자 잔액에서 `recipient`에게 보냅니다. 토큰 소유자의 토큰을 보내도록 허락받은 주소가 이 토큰 전송 트랜잭션을 실행할 수 있습니다. Thus, the approved one should be the sender of this transaction whose address must be given at `sendParam.from` or `kip7.options.from`. Without `sendParam.from` nor `kip7.options.from` being provided, an error would occur.

트랜잭션 전송은 트랜잭션 발신자에게 트랜잭션 수수료를 부과함을 유의하십시오.

**매개변수**

| 명칭        | 형식                                    | 설명                                                                                                                                                                            |
| --------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sender    | 문자열                                   | 토큰을 소유한 계정 주소입니다. 이 계정 주소 잔액에서 allowance(kip7Instance.approve)를 사용해 토큰이 보내집니다.                                                                                                |
| recipient | 문자열                                   | The address of the account to receive the token.                                                                                                                              |
| amount    | BigNumber &#124; string &#124; number | 전송할 토큰 수량입니다.                                                                                                                                                                 |
| sendParam | object                                | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** The `amount` parameter accepts `number` type but if the fed value were out of the range capped by number.MAX_SAFE_INTEGER, it might cause an unexpected result or error. 이 경우, `BigNumber` 타입 값 사용이 권장되며, 특히 `uint256` 크기의 숫자 입력은 `BigNumber` 타입 값을 사용하는 것이 좋습니다.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given
> kip7.transferFrom('0x{address in hex}', '0x{address in hex}', 10000, { from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0x3adec238e06a9e8d5fa09fc1e1d7c8748b64d07e89678d27e8a379a12a34974f',
    blocknumber: 2331,
    contractAddress: null,
    from: '0x01958c62ab4aec7fc282bec9491da0ef7f830ac2',
    ...
    status: true,
    to: '0x3d5eb40665d25aaa4160023c4278fa6a94ba4acb',
    ...
    events: {
        Transfer: {
            address: '0x3D5EB40665D25aAa4160023C4278FA6A94BA4aCb',
            blocknumber: 2331,
            transactionHash: '0x5b2232b68681f19d9b6fcd6fb03964ef105912fecb772c11c8ec9fc906be4cbf',
            transactionIndex: 0,
            blockHash: '0x3adec238e06a9e8d5fa09fc1e1d7c8748b64d07e89678d27e8a379a12a34974f',
            logIndex: 0,
            id: 'log_ae57b7a0',
            returnValues: {
                '0': '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                '1': '0x49ff9cb8BB8CA10D7f6E1094b2Ba56c3C2DBA231',
                '2': '10000',
                from: '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                to: '0x49ff9cb8BB8CA10D7f6E1094b2Ba56c3C2DBA231',
                value: '10000'
            },
            event: 'Transfer',
            signature: '0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef',
            raw: {
                data: '0x0000000000000000000000000000000000000000000000000000000000002710',
                topics: [ '0xddf25...', '0x00...676', '0x00...231' ]
            },
        },
        Approval: {
            address: '0x3D5EB40665D25aAa4160023C4278FA6A94BA4aCb',
            blocknumber: 2331,
            transactionHash: '0x5b2232b68681f19d9b6fcd6fb03964ef105912fecb772c11c8ec9fc906be4cbf',
            transactionIndex: 0,
            blockHash: '0x3adec238e06a9e8d5fa09fc1e1d7c8748b64d07e89678d27e8a379a12a34974f',
            logIndex: 1,
            id: 'log_cee37d26',
            returnValues: {
                '0': '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                '1': '0x01958c62aB4aEC7fC282bEc9491dA0EF7F830AC2',
                '2': '0',
                owner: '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                spender: '0x01958c62aB4aEC7fC282bEc9491dA0EF7F830AC2',
                value: '0'
            },
            event: 'Approval',
            signature: '0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925',
            raw: {
                data: '0x0000000000000000000000000000000000000000000000000000000000000000',
                topics: [ '0x8c5be...', '0x00...676', '0x00...ac2' ]
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.transferFrom('0x{address in hex}', '0x{address in hex}', 10000).then(console.log)
```

## kip7.safeTransferFrom <a id="kip7-safetransferfrom"></a>

```javascript
kip7.safeTransferFrom(sender, recipient, amount [, data] [, sendParam])
```
주어진 `amount`만큼의 토큰을 토큰 소유자 잔액에서 `recipient`에게 안전하게 보냅니다. 토큰 소유자의 토큰을 보내도록 허락받은 주소가 이 토큰 전송 트랜잭션을 실행할 수 있습니다. Thus, the approved one should be the sender of this transaction whose address must be given at `sendParam.from` or `kip7.options.from`. Without `sendParam.from` nor `kip7.options.from` being provided, an error would occur.

만약 토큰 수신자 주소가 컨트랙트 주소라면, 컨트랙트는 반드시 [IKIP7Receiver.onKIP7Received](https://kips.klaytn.com/KIPs/kip-7#wallet-interface)를 구현했어야 합니다. 그렇지 않으면, 전송은 거부됩니다.

트랜잭션 전송은 트랜잭션 발신자에게 트랜잭션 수수료를 부과함을 유의하십시오.

**매개변수**

| 명칭        | 형식                                    | 설명                                                                                                                                                                            |
| --------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sender    | 문자열                                   | 토큰을 소유한 계정 주소입니다. 이 계정 주소 잔액에서 allowance(kip7Instance.approve)를 사용해 토큰이 보내집니다.                                                                                                |
| recipient | 문자열                                   | The address of the account to receive the token.                                                                                                                              |
| amount    | BigNumber &#124; string &#124; number | 전송할 토큰 수량입니다.                                                                                                                                                                 |
| data      | Buffer &#124; string &#124; number    | (optional) The optional data to send along with the call.                                                                                                                     |
| sendParam | object                                | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** The `amount` parameter accepts `number` type but if the fed value were out of the range capped by number.MAX_SAFE_INTEGER, it might cause an unexpected result or error. 이 경우, `BigNumber` 타입 값 사용이 권장되며, 특히 `uint256` 크기의 숫자 입력은 `BigNumber` 타입 값을 사용하는 것이 좋습니다.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given (without data)
> kip7.safeTransferFrom('0x{address in hex}', '0x{address in hex}', 10000, { from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0x0d641b9cebb032f10348288623898f8aa319faa0845c5b3b7a59ac397a6a218b',
    blocknumber: 2404,
    contractAddress: null,
    from: '0x090937f5c9b83d961da29149a3c37104bc5e71b3',
    ...
    status: true,
    to: '0xe4aeba6306b0df023aa4b765960fa59dbe925950',
    ...
    events: {
            Transfer: {
                    address: '0xe4AeBa6306b0Df023AA4b765960fA59dbE925950',
                    blocknumber: 2404,
                    transactionHash: '0xed8c33facaea963f57c268134aaab48fa765e7298fd70d4bc796b1e93c12ad45',
                    transactionIndex: 0,
                    blockHash: '0x0d641b9cebb032f10348288623898f8aa319faa0845c5b3b7a59ac397a6a218b',
                    logIndex: 0,
                    id: 'log_5eaef2c3',
                    returnValues: {
                            '0': '0xC2C84328845A36Fe0c4DcEf370d24ec80cF85221',
                            '1': '0x67B092d09B5e94fed58609777cc7Ac9193553B73',
                            '2': '10000',
                            from: '0xC2C84328845A36Fe0c4DcEf370d24ec80cF85221',
                            to: '0x67B092d09B5e94fed58609777cc7Ac9193553B73',
                            value: '10000',
                    },
                    event: 'Transfer',
                    signature: '0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef',
                    raw: {
                            data: '0x0000000000000000000000000000000000000000000000000000000000002710',
                            topics: [ '0xddf25...', '0x00...221', '0x00...b73' ],
                    },
            },
            Approval: {
                    address: '0xe4AeBa6306b0Df023AA4b765960fA59dbE925950',
                    blocknumber: 2404,
                    transactionHash: '0xed8c33facaea963f57c268134aaab48fa765e7298fd70d4bc796b1e93c12ad45',
                    transactionIndex: 0,
                    blockHash: '0x0d641b9cebb032f10348288623898f8aa319faa0845c5b3b7a59ac397a6a218b',
                    logIndex: 1,
                    id: 'log_3f3aedf8',
                    returnValues: {
                            '0': '0xC2C84328845A36Fe0c4DcEf370d24ec80cF85221',
                            '1': '0x090937f5C9B83d961da29149a3C37104Bc5e71B3',
                            '2': '0',
                            owner: '0xC2C84328845A36Fe0c4DcEf370d24ec80cF85221',
                            spender: '0x090937f5C9B83d961da29149a3C37104Bc5e71B3',
                            value: '0',
                    },
                    event: 'Approval',
                    signature: '0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925',
                    raw: {
                            data: '0x0000000000000000000000000000000000000000000000000000000000000000',
                            topics: [ '0x8c5be...', '0x00...221', '0x00...1b3' ],
                    },
            },
    },
}

// Send via a sendParam object with the from field given (with data)
> kip7.safeTransferFrom('0x{address in hex}', '0x{address in hex}', 11, '0x1234', { from: '0x{address in hex}' }).then(console.log)

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.safeTransferFrom('0x{address in hex}', '0x{address in hex}', 11).then(console.log)
```

## kip7.mint <a id="kip7-mint"></a>

```javascript
kip7.mint(account, amount [, sendParam])
```
`amount`만큼의 토큰을 만들어 `account`에게 발행합니다. 이 함수는 토큰 총 공급량을 증가시킵니다.

이 메서드는 Klaytn 네트워크에 트랜잭션을 전송하며 트랜잭션 수수료가 트랜잭션 발신자에게 부과됨을 참고하시기 바랍니다.

**매개변수**

| 명칭           | 형식                                    | 설명                                                                                                                                                                            |
| ------------ | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 계정 (Account) | 문자열                                   | 토큰이 발행될 계정 주소입니다.                                                                                                                                                             |
| amount       | BigNumber &#124; string &#124; number | 발행될 토큰 수량입니다.                                                                                                                                                                 |
| sendParam    | object                                | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** The `amount` parameter accepts `number` type but if the fed value were out of the range capped by number.MAX_SAFE_INTEGER, it might cause an unexpected result or error. 이 경우, `BigNumber` 타입 값 사용이 권장되며, 특히 `uint256` 크기의 숫자 입력은 `BigNumber` 타입 값을 사용하는 것이 좋습니다.

**NOTE** If `sendParam.from` or `kip7.options.from` were given, it should be a minter with MinterRole.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given 
> kip7.mint('0x{address in hex}', 10000, { from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0x71e1c7c9de471ed9eb9ec2aca09beb63a654e21514b2b8d25ec93f34b810a709',
    blocknumber: 8466,
    contractAddress: null,
    from: '0x2f7dc98bd93a0544b03d6ff428a6f4ae04b32676',
    ...
    status: true,
    to: '0x54e9ad10ffcbcc2384863157c851a75a31c1e925',
    ...
    events: {
        Transfer: {
            address: '0x54e9Ad10FFcBCc2384863157c851A75a31C1E925',
            blocknumber: 8466,
            transactionHash: '0xef1db1544d0ba70aa06b77599a8421cee2270703cff7d0233bd09ab3561ab49a',
            transactionIndex: 0,
            blockHash: '0x71e1c7c9de471ed9eb9ec2aca09beb63a654e21514b2b8d25ec93f34b810a709',
            logIndex: 0,
            id: 'log_151f8e90',
            returnValues: {
                '0': '0x0000000000000000000000000000000000000000',
                '1': '0x4756D3c2A3DC61450D949BD9bF702b4209Fc15a0',
                '2': '10000',
                from: '0x0000000000000000000000000000000000000000',
                to: '0x4756D3c2A3DC61450D949BD9bF702b4209Fc15a0',
                value: '10000',
            },
            event: 'Transfer',
            signature: '0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef',
            raw: {
                data: '0x0000000000000000000000000000000000000000000000000000000000002710',
                topics: [ '0xddf25...', '0x00...000', '0x00...5a0' ],
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.mint('0x{address in hex}', 10000).then(console.log)
```


## kip7.addMinter <a id="kip7-addminter"></a>

```javascript
kip7.addMinter(account [, sendParam])
```
계정을 발행자에 추가합니다. 발행자는 토큰을 발행하도록 허락된 계정입니다.

이 메서드는 Klaytn 네트워크에 트랜잭션을 전송하며 트랜잭션 수수료가 트랜잭션 발신자에게 부과됨을 참고하시기 바랍니다.

**매개변수**

| 명칭           | 형식     | 설명                                                                                                                                                                            |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 계정 (Account) | 문자열    | 발행자에 추가될 계정 주소입니다.                                                                                                                                                            |
| sendParam    | object | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** If `sendParam.from` or `kip7.options.from` were given, it should be a minter.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given 
> kip7.addMinter('0x{address in hex}', { from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0x169db7e80c954f7d95bbb6a5ef3065190e842d515485e1679f8f3027d1b2975f',
    blocknumber: 9593,
    contractAddress: null,
    from: '0x2f7dc98bd93a0544b03d6ff428a6f4ae04b32676',
    ...
    status: true,
    to: '0x9e2851aff794e69c58e112a3beacbf0de6587f6b',
    ...
    events: {
        MinterAdded: {
            address: '0x9E2851Aff794E69C58E112a3beacbF0De6587f6b',
            blocknumber: 9593,
            transactionHash: '0x11c86fe739ce3f8e6f93f5de87c9626c7cd032dd5e119171f9ec821292cd68e9',
            transactionIndex: 0,
            blockHash: '0x169db7e80c954f7d95bbb6a5ef3065190e842d515485e1679f8f3027d1b2975f',
            logIndex: 0,
            id: 'log_d93efbcd',
            returnValues: {
                '0': '0x823EA6Eb41985218D478C07E77cFBdAd233569C5',
                account: '0x823EA6Eb41985218D478C07E77cFBdAd233569C5',
            },
            event: 'MinterAdded',
            signature: '0x6ae172837ea30b801fbfcdd4108aa1d5bf8ff775444fd70256b44e6bf3dfc3f6',
            raw: {
                data: '0x',
                topics: [ '0x6ae17...', '0x00...9c5' ],
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.addMinter('0x{address in hex}').then(console.log)
```


## kip7.renounceMinter <a id="kip7-renounceminter"></a>

```javascript
kip7.renounceMinter([sendParam])
```
토큰 발행 권한을 포기합니다. 오직 발행자 주소만이 발행 권한을 포기할 수 있습니다.

이 메서드는 Klaytn 네트워크에 트랜잭션을 전송하며 트랜잭션 수수료가 트랜잭션 발신자에게 부과됨을 참고하시기 바랍니다.

**매개변수**

| 명칭        | 형식     | 설명                                                                                                                                                                            |
| --------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sendParam | object | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** If `sendParam.from` or `kip7.options.from` were given, it should be a minter with MinterRole.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given 
> kip7.renounceMinter({ from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0xc1d96a519d9a31a1dab77111af0de73241aa212722859062a96dc3115a2eca23',
    blocknumber: 9996,
    contractAddress: null,
    from: '0x34b91db0f4c7d1381fdf054cc3d0c433b19fca16',
    ...
    status: true,
    to: '0xeba808dcd0fdbfc21a99961be42665f351487f52',
    ...
    events: {
        MinterRemoved: {
            address: '0xebA808dCD0Fdbfc21a99961BE42665f351487F52',
            blocknumber: 9996,
            transactionHash: '0x52328e3cfb8061915d000dc308ffd67650fa36cf4560f1fb12fdb28a7c903ac9',
            transactionIndex: 0,
            blockHash: '0xc1d96a519d9a31a1dab77111af0de73241aa212722859062a96dc3115a2eca23',
            logIndex: 0,
            id: 'log_bd3a8e46',
            returnValues: {
                '0': '0x34b91Db0F4c7D1381FdF054cc3D0c433B19fCa16',
                account: '0x34b91Db0F4c7D1381FdF054cc3D0c433B19fCa16',
            },
            event: 'MinterRemoved',
            signature: '0xe94479a9f7e1952cc78f2d6baab678adc1b772d936c6583def489e524cb66692',
            raw: {
                data: '0x',
                topics: [ '0xe9447...', '0x00...a16' ],
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.renounceMinter().then(console.log)
```


## kip7.burn <a id="kip7-burn"></a>

```javascript
kip7.burn(amount [, sendParam])
```
`amount`만큼의 토큰을 트랜잭션 발신자 잔액에서 제거합니다. Without `sendParam.from` nor `kip7.options.from` being provided, an error would occur.

이 메서드는 Klaytn 네트워크에 트랜잭션을 전송하며 트랜잭션 수수료가 트랜잭션 발신자에게 부과됨을 참고하시기 바랍니다.

**매개변수**

| 명칭        | 형식                                    | 설명                                                                                                                                                                            |
| --------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| amount    | BigNumber &#124; string &#124; number | 제거할 토큰 수량입니다.                                                                                                                                                                 |
| sendParam | object                                | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** The `amount` parameter accepts `number` type but if the fed value were out of the range capped by number.MAX_SAFE_INTEGER, it might cause an unexpected result or error. 이 경우, `BigNumber` 타입 값 사용이 권장되며, 특히 `uint256` 크기의 숫자 입력은 `BigNumber` 타입 값을 사용하는 것이 좋습니다.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given 
> kip7.burn(1000, { from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0x7cf9e982510d17a2fd5fca3e7a6f9ce5a25a9da6ba81d51b33129fb7fb93e0ae',
    blocknumber: 10495,
    contractAddress: null,
    from: '0x2f7dc98bd93a0544b03d6ff428a6f4ae04b32676',
    ...
    status: true,
    to: '0x0f681dbc120d9d3be997565626cd87f049f5c405',
    ...
    events: {
        Transfer: {
            address: '0x0f681Dbc120D9d3BE997565626CD87F049f5C405',
            blocknumber: 10495,
            transactionHash: '0x4f2de0b4310c40eeef20ae8e8d129d209195975792de86e1cd00f2345789c9f7',
            transactionIndex: 0,
            blockHash: '0x7cf9e982510d17a2fd5fca3e7a6f9ce5a25a9da6ba81d51b33129fb7fb93e0ae',
            logIndex: 0,
            id: 'log_20f6c253',
            returnValues: {
                '0': '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                '1': '0x0000000000000000000000000000000000000000',
                '2': '1000',
                from: '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                to: '0x0000000000000000000000000000000000000000',
                value: '1000',
            },
            event: 'Transfer',
            signature: '0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef',
            raw: {
                data: '0x00000000000000000000000000000000000000000000000000000000000003e8',
                topics: [ '0xddf25...', '0x00...676', '0x00...000' ],
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.burn(1000).then(console.log)
```


## kip7.burnFrom <a id="kip7-burnfrom"></a>

```javascript
kip7.burnFrom(account, amount [, sendParam])
```
`account`에서 주어진 토큰 수량을 제거합니다. The allowance of the sender specified in `sendParam.from` or `kip7.options.from` is reduced alongside the balance of `account`.

이 메서드는 Klaytn 네트워크에 트랜잭션을 전송하며 트랜잭션 수수료가 트랜잭션 발신자에게 부과됨을 참고하시기 바랍니다.

**매개변수**

| 명칭           | 형식                                    | 설명                                                                                                                                                                            |
| ------------ | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 계정 (Account) | 문자열                                   | 토큰을 소유한 계정 주소입니다. 이 계정 주소 잔액에서 allowance(kip7Instance. approve)를 사용해 토큰이 제거됩니다.                                                                                               |
| amount       | BigNumber &#124; string &#124; number | 제거할 토큰 수량입니다.                                                                                                                                                                 |
| sendParam    | object                                | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** The `amount` parameter accepts `number` type but if the fed value were out of the range capped by number.MAX_SAFE_INTEGER, it might cause an unexpected result or error. 이 경우, `BigNumber` 타입 값 사용이 권장되며, 특히 `uint256` 크기의 숫자 입력은 `BigNumber` 타입 값을 사용하는 것이 좋습니다.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given 
> kip7.burnFrom('0x{address in hex}', 1000, { from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0xcd9f3d00856a056e54697cde2621d8af779c11378c422700510d6ebf65bea0a8',
    blocknumber: 11371,
    contractAddress: null,
    from: '0x1b7bdfcfb0008d0c958da13f2dc30388271e9ef0',
    ...
    status: true,
    to: '0x50fafa2b059d26c47d26c35ccb3cd3b856ecc852',
    ...
    events: {
        Transfer: {
            address: '0x50fAFa2B059d26C47D26c35Ccb3Cd3b856Ecc852',
            blocknumber: 11371,
            transactionHash: '0xed37eafc35272bd7c45695b4b94c578c681a1800b1612ca82d0e4e595e947f27',
            transactionIndex: 0,
            blockHash: '0xcd9f3d00856a056e54697cde2621d8af779c11378c422700510d6ebf65bea0a8',
            logIndex: 0,
            id: 'log_a7263788',
            returnValues: {
                '0': '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                '1': '0x0000000000000000000000000000000000000000',
                '2': '10000',
                from: '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                to: '0x0000000000000000000000000000000000000000',
                value: '10000',
            },
            event: 'Transfer',
            signature: '0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef',
            raw: {
                data: '0x0000000000000000000000000000000000000000000000000000000000002710',
                topics: [ '0xddf25...', '0x00...676', '0x00...000' ],
            },
        },
        Approval: {
            address: '0x50fAFa2B059d26C47D26c35Ccb3Cd3b856Ecc852',
            blocknumber: 11371,
            transactionHash: '0xed37eafc35272bd7c45695b4b94c578c681a1800b1612ca82d0e4e595e947f27',
            transactionIndex: 0,
            blockHash: '0xcd9f3d00856a056e54697cde2621d8af779c11378c422700510d6ebf65bea0a8',
            logIndex: 1,
            id: 'log_4ca1aac4',
            returnValues: {
                '0': '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                '1': '0x1B7BdfCFb0008D0C958dA13F2dc30388271E9eF0',
                '2': '0',
                owner: '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                spender: '0x1B7BdfCFb0008D0C958dA13F2dc30388271E9eF0',
                value: '0',
            },
            event: 'Approval',
            signature: '0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925',
            raw: {
                data: '0x0000000000000000000000000000000000000000000000000000000000000000',
                topics: [ '0x8c5be...', '0x00...676', '0x00...ef0' ],
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.burnFrom('0x{address in hex}', 1000).then(console.log)
```


## kip7.addPauser <a id="kip7-addpauser"></a>

```javascript
kip7.addPauser(account [, sendParam])
```
계정에게 컨트랙트를 중지할 권한을 추가합니다.

이 메서드는 Klaytn 네트워크에 트랜잭션을 전송하며 트랜잭션 수수료가 트랜잭션 발신자에게 부과됨을 참고하시기 바랍니다.

**매개변수**

| 명칭           | 형식     | 설명                                                                                                                                                                            |
| ------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 계정 (Account) | 문자열    | 컨트랙트 중지 권한을 가질 계정 주소입니다.                                                                                                                                                      |
| sendParam    | object | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** If `sendParam.from` or `kip7.options.from` were given, it should be a pauser with PauserRole.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given 
> kip7.addPauser('0x{address in hex}', { from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0x14bcefa90f95f5db03ed9c43a77ae910b57960f4f44c786e3a650a8ad163f67a',
    blocknumber: 16524,
    contractAddress: null,
    from: '0x2f7dc98bd93a0544b03d6ff428a6f4ae04b32676',
    ...
    status: true,
    to: '0x31fee792a85ff4d714f47a151975b4979cb47308',
    ...
    events: {
        PauserAdded: {
            address: '0x31fee792A85ff4D714F47A151975b4979CB47308',
            blocknumber: 16524,
            transactionHash: '0x9bd0cba9f5fdc3fdae4b9f40f46f11bf42314ca2518724e78be266d46a8a9f96',
            transactionIndex: 0,
            blockHash: '0x14bcefa90f95f5db03ed9c43a77ae910b57960f4f44c786e3a650a8ad163f67a',
            logIndex: 0,
            id: 'log_d847b043',
            returnValues: {
                '0': '0x6610B93bAE66F89716C3b010ad39DF476Da9234b',
                account: '0x6610B93bAE66F89716C3b010ad39DF476Da9234b',
            },
            event: 'PauserAdded',
            signature: '0x6719d08c1888103bea251a4ed56406bd0c3e69723c8a1686e017e7bbe159b6f8',
            raw: {
                data: '0x',
                topics: [ '0x6719d...', '0x00...34b' ],
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.addPauser('0x{address in hex}').then(console.log)
```


## kip7.renouncePauser <a id="kip7-renouncepauser"></a>

```javascript
kip7.renouncePauser([sendParam])
```
토큰 중지 권한을 포기합니다. 오직 컨트랙트 중지 권한 소유자 주소만이 중지 권한을 포기할 수 있습니다.

이 메서드는 Klaytn 네트워크에 트랜잭션을 전송하며 트랜잭션 수수료가 트랜잭션 발신자에게 부과됨을 참고하시기 바랍니다.

**매개변수**

| 명칭        | 형식     | 설명                                                                                                                                                                            |
| --------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sendParam | object | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** If `sendParam.from` or `kip7.options.from` were given, it should be a pauser with PauserRole.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given 
> kip7.renouncePauser({ from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0xc0b1b4914ddc8d74e8034fe86ede1b5b88a2c16ee4d678e58fac325c589713f6',
    blocknumber: 16567,
    contractAddress: null,
    from: '0x5934a0c01baa98f3457981b8f5ce6e52ac585578',
    ...
    status: true,
    to: '0x31fee792a85ff4d714f47a151975b4979cb47308',
    ...
    events: {
        PauserRemoved: {
            address: '0x31fee792A85ff4D714F47A151975b4979CB47308',
            blocknumber: 16567,
            transactionHash: '0xefc93382f5609531dd16f644cf6a3b8e086c623a9fb8038984662f7260482df6',
            transactionIndex: 0,
            blockHash: '0xc0b1b4914ddc8d74e8034fe86ede1b5b88a2c16ee4d678e58fac325c589713f6',
            logIndex: 0,
            id: 'log_e9518d2f',
            returnValues: {
                '0': '0x5934a0c01baA98F3457981b8f5ce6E52ac585578',
                account: '0x5934a0c01baA98F3457981b8f5ce6E52ac585578',
            },
            event: 'PauserRemoved',
            signature: '0xcd265ebaf09df2871cc7bd4133404a235ba12eff2041bb89d9c714a2621c7c7e',
            raw: {
                data: '0x',
                topics: [ '0xcd265...', '0x00...578' ],
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.renouncePauser().then(console.log)
```


## kip7.pause <a id="kip7-pause"></a>

```javascript
kip7.pause([sendParam])
```
토큰 전송과 관련된 기능들을 중지합니다.

이 메서드는 Klaytn 네트워크에 트랜잭션을 전송하며 트랜잭션 수수료가 트랜잭션 발신자에게 부과됨을 참고하시기 바랍니다.

**매개변수**

| 명칭        | 형식     | 설명                                                                                                                                                                            |
| --------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sendParam | object | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** If `sendParam.from` or `kip7.options.from` were given, it should be a pauser with PauserRole.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given 
> kip7.pause({ from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0xcd5e787e738a6197df871f0d651f2a9149d5ed03fdf62e918c4eed03003ea539',
    blocknumber: 18218,
    contractAddress: null,
    from: '0x2f7dc98bd93a0544b03d6ff428a6f4ae04b32676',
    ...
    status: true,
    to: '0xfc83abf47d232739dab9610c46b3f10c8022b3ef',
    ...
    events: {
        Paused: {
            address: '0xFc83ABF47d232739dAb9610C46B3F10C8022b3eF',
            blocknumber: 18218,
            transactionHash: '0x0e660b8c49e8212a69f2d68324e105b4295b534d22ac0b70263d3e54d429d1bb',
            transactionIndex: 0,
            blockHash: '0xcd5e787e738a6197df871f0d651f2a9149d5ed03fdf62e918c4eed03003ea539',
            logIndex: 0,
            id: 'log_2ab0db96',
            returnValues: {
                '0': '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                account: '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
            },
            event: 'Paused',
            signature: '0x62e78cea01bee320cd4e420270b5ea74000d11b0c9f74754ebdbfc544b05a258',
            raw: {
                data: '0x0000000000000000000000002f7dc98bd93a0544b03d6ff428a6f4ae04b32676',
                topics: ['0x62e78cea01bee320cd4e420270b5ea74000d11b0c9f74754ebdbfc544b05a258'],
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.pause().then(console.log)
```


## kip7.unpause <a id="kip7-unpause"></a>

```javascript
kip7.unpause([sendParam])
```
중지된 컨트랙트를 재개합니다.

이 메서드는 Klaytn 네트워크에 트랜잭션을 전송하며 트랜잭션 수수료가 트랜잭션 발신자에게 부과됨을 참고하시기 바랍니다.

**매개변수**

| 명칭        | 형식     | 설명                                                                                                                                                                            |
| --------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sendParam | object | (optional) An object with defined parameters for sending a transaction. For more information about sendParam, refer to the parameter description of [approve](#kip7-approve). |

**NOTE** If `sendParam.from` or `kip7.options.from` were given, it should be a pauser with PauserRole.

**리턴값**

`Promise` returns `object` - The receipt containing the result of the transaction execution. If you want to know about the properties inside the receipt object, see the description of [getTransactionReceipt][]. KIP7 인스턴스의 영수증은 'logs' 속성 대신에 ABI로 파싱된 'events' 속성을 가지고 있습니다.

**예시**

```javascript
// Send via a sendParam object with the from field given 
> kip7.unpause({ from: '0x{address in hex}' }).then(console.log)
{
    blockHash: '0xa45194ba608a0a00152f974fb1388ced326522979f4b8f19c3fab3083f1339ac',
    blocknumber: 18239,
    contractAddress: null,
    from: '0x2f7dc98bd93a0544b03d6ff428a6f4ae04b32676',
    ...
    status: true,
    to: '0xfc83abf47d232739dab9610c46b3f10c8022b3ef',
    ...
    events: {
        Unpaused: {
            address: '0xFc83ABF47d232739dAb9610C46B3F10C8022b3eF',
            blocknumber: 18239,
            transactionHash: '0x449dff9d7970bfe326091516ebb22aeaefb0bda59bc4e2577467618863e36c99',
            transactionIndex: 0,
            blockHash: '0xa45194ba608a0a00152f974fb1388ced326522979f4b8f19c3fab3083f1339ac',
            logIndex: 0,
            id: 'log_9c5a3823',
            returnValues: {
                '0': '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
                account: '0x2f7Dc98Bd93A0544B03d6ff428a6f4ae04b32676',
            },
            event: 'Unpaused',
            signature: '0x5db9ee0a495bf2e6ff9c91a7834c1ba4fdd244a5e8aa4e537bd38aeae4b073aa',
            raw: {
                data: '0x0000000000000000000000002f7dc98bd93a0544b03d6ff428a6f4ae04b32676',
                topics: ['0x5db9ee0a495bf2e6ff9c91a7834c1ba4fdd244a5e8aa4e537bd38aeae4b073aa'],
            },
        },
    },
}

// Using kip7.options.from
// If the value of kip7.options.from is set, this value is used as the default value 
// unless you specify `from` in the sendParam object when sending a transaction with a kip7 instance.
> kip7.options.from = '0x{address in hex}'
> kip7.unpause().then(console.log)
```

[getTransactionReceipt]: ../caver.rpc/klay.md#caver-rpc-klay-gettransactionreceipt