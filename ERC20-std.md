# **基于ERC20的代币基础版本**

## **创建需要参数**

 参数名 | 类型 | 参数意义 | 举例 | 备注
 --- | --- | --- | --- | ---
**name** | string | 代币名称 | GammaTokenV3 | 必须英文字母或者数字
**symbol** | string | 代币符号 | GMTV3 | 必须大写英文字母或者数字
**totalSupply** | int | 发行总量 | 300000 | 必须正整数
**decimal** | int | 代币精度，小数点后保留的位数。 | 4 | 如果decimal为4，则代币最小额度只能到0.0001。必须小于20的正整数

---

## **标准方法**

### **name()**  
用来获取代币的名称  

输入：无  
输出：代币的名称

示例：
```javascript
> c.name()
"GammaTokenV3"
```

### **symbol()** 
获取代币的符号  

输入：无   
输出：代币的缩写符号

示例：
```javascript
> c.symbol()
"GMTV3"
```

### **decimals()**  
获取代币的最小精度   

输入：无    
输出：代币的最小精度，小数点后的精确位数。 

示例：
```javascript
> c.decimals()
4
```

### **owner()**  
获取代币系统的拥有者地址  

输入：无  
输出：此代币系统的当前拥有人地址

示例：
```javascript
> c.owner()
"0x11d71c6a0d3e66c0234bfdfcb8c148ab29cb0b94"
``` 


### **totalSupply()**  
获取发行总量  

输入：无  
输出：代币的发行总量

示例：
```javascript
> c.totalSupply()
3000000000
```  

备注：因为之前设置的 `decimal` 为 4 且系统只支持正整数，所以 `totalSupply` 输出的发行总量比设置的发行总量 `300000` 要多出四个零。

### **balanceOf(address)**  
获取某个地址的代币余额   

输入：账号地址   
输出：某个账号地址的代币余额 

示例：
```javascript
> c.balanceOf("0x7855ae187cf378701d0440080db908302a41bfe2")
3000000000
```

### **transfer(address, int)**  
向一个地址转账

输入：收款地址，转账金额   
输出：transaction hash 

示例：
```javascript
> c.transfer("0x7855ae187cf378701d0440080db908302a41bfe2", 1000, {from: "addr_from"})
"0x0817754496fd6263fe973ebebf982c6a45b21e79f1330c8aeec8229f73ace80e"
```

### **approve(address, int)**  
代币拥有者通过approve授权另一个账号，被授权后的账号可以 获得代币拥有者 的部分代币支配权。

输入：被授权的账号地址，可支配的代币的金额   
输出：transaction hash

示例：
```javascript
> c.approve("addr_a",100000, {from:"addr_from"})
"0x40ca2ad0a986aa244d51ffb4c6538baf1cd7aee6db227fd3f820e20931822d3d"
```

备注：获得授权后，`addr_a` 账号就可以支配 `addr_from` 的部分代币，这部分代币的金额为 `100000`。

### **transferFrom(address, address, int)**  
获得授权的账号地址行使自己的权利，将部分代币从给予其权力的地址转账到任意其他地址。每次行使权利都会使其可支配的代币金额减少。  

输入：转账源地址，转账目标地址，转账金额   
输出：transaction hash 

示例：
```javascript
c.transferFrom("addr_from", "addr_to", 1000, {from:"addr_a"})
"0x16905fc50120be5c88ff30a649aa3a052f0b88663a98a72fff88c62c9b3b5921"
```

备注：`addr_a` 行使自己的权力，将 `1000` 代币从 `addr_from` 转账到 `addr_to`。因为之前通过 approve 方法获得了`addr_from`名下的 100000 代币的支配权，所以通过这次 `transferFrom` 方法的调用，`addr_a` 实际可支配的 `addr_from` 名下的代币减少为 `100000 - 1000 = 99000`.

### **allowance(address, address)**  
查看一个账户可支配的另一个账户代币的金额  

输入：代币拥有者地址，获得授权的地址。   
输出：授权地址的可支配代币金额 

示例：
```javascript
c.allowance("addr_token_owner", "addr_spender")
99000
```

备注：查看 `addr_spender`账号可以支配的 `addr_token_owner` 账号名下的代币的金额。

## **附加方法**

### **increaseApproval(address, int)**
增加授权金额

输入：被授权的账号地址，增加的金额   
输出：transaction hash

示例：
```javascript
> c.increaseApproval("addr_spender", 10000, {from: "addr_token_owner"})
"0xd6f4f2d1fe0026b7ae1c18d271813f31c68b57212a19a183ba6a540938f71565"
```

### **decreaseApproval(address, int)**
减少授权金额

输入：被授权的账号地址，增加的金额  
输出：transaction hash

示例：
```javascript
> c.decreaseApproval("addr_spender", 10000, {from: "addr_token_owner"})
"0x11a52d252d1551b470de7fc3de77e3ced67798df09d6589c630f35b75694f9e1"
```

### **transferOwnership(address)**
更改代币系统的拥有者

输入：新的系统拥有者的地址  
输出：transaction hash

示例：
```javascript
> c.transferOwnership("addr_new_owner", {from: "addr_owner"})
"0x55a1d3ecc0f6dfdd474abb61571313ed110997e2a91563e80532b88625aebc11"
```

备注：此方法只能被系统拥有人调用。

### **mint(int)**
增加代币的发行金额，并将新增的金额添加到当前系统拥有人的账户上。

输入：新增发行金额   
输出：transaction hash

示例：
```javascript
> c.mint(100000)
"0x81505f64cb48a0d5f1db7b86141b5d631a0ede0b4adbc350191b865dd1644090"
```

备注：此方法只能被系统拥有人调用。

### **burn(int)**
减少代币的发行金额，同时减少当前系统拥有人的账户的金额。

输入：减少的发行金额   
输出：transaction hash

示例：
```javascript
> c.burn(100000)
"0xce1af0b4c2f865fb1975ea3fb2d95e3b30020f75b4ce41cbea3c298223562043"
```

备注：此方法只能被系统拥有人调用。




