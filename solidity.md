# solidity
solidity可以被视为一种常规的面相对象语言。它与常规语言的差异都是因为EVM的特性导致的。

与常规语言的差异:

1.EVM是一种基于栈的虚拟机，所以solidity编译出的指令集是EVM专用的一种指令集，这种指令集数量相比传统的基于寄存器的语言要少很多

2.EVM上的长期存储（storage）不保存key，所以solidity中的mapping是不支持枚举key或者value的，也不支持计数，这与其他语言都不一样

3.在solidity上编程要时刻考虑storage，memory，变量存储在哪至关重要

4.solidity中的library是无法访问状态变量的（无法读或写storage的内容）

5.solidity中的call，delegatecall，staticcall  
* staticcall
    staticcall无法改变状态变量，无法发送和支付货币，是一个功能更受限的call

* call
    call指令是调用另一个合约，控制权也交给另一个合约，上下文也变成了另一个合约的上下文，直到call的合约结束运行并且返回时，才回到这个合约的上下文

* delegatecall
    delegatecall与call不同的是，上下文不会变化。另一个合约中的代码在执行时，用的是调用者的上下文，另一个合约被调用的代码中某一个的作用可能是“修改位置在0的存储槽位的值为1”，那么调用者的位置在0的存储槽位的值会被变为1，而被调用者的存储槽位不会有任何变化，下面是例子

```solidity
contract A {
    uint256 public a;
    address delegate;

    construct(address _delegate) {
        delegate = _delegate;
    }
    // 实际会改变a的值
    function setAToSometing(uint256 _a) public returns (bool){
        (bool success,) = delegate.delegatecall(abi.encodeWithSignature("setValues(uint256)", _a));
        return success;
    }

    // 实际会改变b的值
    function setBToSometion(uint256 _a) public returns (bool){
        (bool success, ) = delegate.call(abi.encodeWithSignature("setValues(uint256)", _a));
        return success;
    }
}

contract B {
    uint256 public b;

    function setValues(uint256 _b) public {
        b = _b;
    }
}

```






