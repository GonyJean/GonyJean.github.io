---
title: solidity-构建一个去中心化投票系统
date: 2024-03-9 11:57:21
tags: [solidity, 合约, web3]
cover: [/images/solidityLogo.png]
banner:
  type: img
  bgurl: [/images/solidityLogo.png]
category: [web3]
single_column: true
---
# solidity-构建一个去中心化投票系统

1. 创建一个 Voting 合约  

```js
contract Voting {}
```

2. 构建结构体

```js
contract Voting {
    struct Vote {
        address voter;
        bool choice;
    }
    struct Poll {
        string question;
        Vote[] votes;
        mapping(address => bool) hasVoted;
    }
    struct pollsListObj {
        string name;
        uint256 yesCount;
        uint256 noCount;
    }

    Poll[] public polls;
}
```

3. 创建投票主体

```js
    function createPoll(string memory question) public {
        // 直接创建新的投票，映射`hasVoted`会自动初始化为空
        Poll storage newPoll = polls.push();
        newPoll.question = question;
    }
```

4. 投票方法

```js
    function vote(uint256 pollIndex, bool choice) public {
        Poll storage poll = polls[pollIndex];
        require(!poll.hasVoted[msg.sender], "You have already voted.");
        Vote memory newVote = Vote({voter: msg.sender, choice: choice});
        poll.votes.push(newVote);
        poll.hasVoted[msg.sender] = true;
    }
```

5. 根据 index 获取某个投票的结果

```js
    function getPollResults(uint256 pollIndex)
        public
        view
        returns (
            uint256 yes,
            uint256 no,
            uint256 CurrentIndex
        )
    {
        Poll storage poll = polls[pollIndex];
        uint256 yesCount = 0;
        uint256 noCount = 0;
        for (uint256 i = 0; i < poll.votes.length; i++) {
            if (poll.votes[i].choice) {
                yesCount++;
            } else {
                noCount++;
            }
        }
        return (yesCount, noCount, pollIndex);
    }
```

6. 获取所有投票-列表

### 踩坑注意

&nbsp;&nbsp;&nbsp;&nbsp;在Solidity中，内存数组（用memory关键字声明的数组）的长度是固定的，这意味着在声明时必须指定其大小，且之后不能改变。
&nbsp;&nbsp;&nbsp;&nbsp;因此，内存数组不支持push操作，这是因为push会增加数组的长度。push方法只适用于存储（storage）数组，因为存储数组的长度是可以动态变化的。
当看到pollsListObj[] memory pollsList这样的声明时，这意味着pollsList是一个内存数组。  
&nbsp;&nbsp;&nbsp;&nbsp;如果想要在函数内部动态地增加数组元素，有两个常见的解决方案：
&nbsp;&nbsp;&nbsp;&nbsp;预先知道数组的大小：如果可以预先知道数组将包含多少元素，就可以在声明时指定这个大小。这在某些情况下是可行的，比如当数组的大小等于某个已知集合的大小时。使用存储数组：如果函数逻辑允许，可以使用存储数组，这样就可以使用push方法。  
&nbsp;&nbsp;&nbsp;&nbsp;不过，通常这意味着数据会永久写入区块链，这可能不是想要的效果。  
&nbsp;&nbsp;&nbsp;&nbsp;针对当前的场景，需要返回函数调用的结果，这时通常使用内存数组是最合适的，因为不需要永久存储这些结果。
如果函数目的是为了收集并返回一些数据，应该在调用此函数之前就知道数组的大小。  
&nbsp;&nbsp;&nbsp;&nbsp;数组的大小应该等于polls.length，因为是为每个投票汇总结果。因此，应该先根据polls.length初始化pollsList数组的大小，然后使用索引赋值而不是push。这是符合Solidity内存数组使用规则的做法。

```js
    // 新增函数以获取所有投票的统计信息
    function getAllPollResults() public view returns (pollsListObj[] memory) {
        pollsListObj[] memory pollsList = new pollsListObj[](polls.length);
        // pollsListObj[] memory pollsList = new pollsListObj[];
        for (uint256 i = 0; i < polls.length; i++) {
            (
                uint256 yesCount,
                uint256 noCount,
                uint256 currentIndex
            ) = getPollResults(i);
            pollsList[currentIndex] = pollsListObj({
                name: polls[currentIndex].question,
                yesCount: yesCount,
                noCount: noCount
            });
        }
        return pollsList;
    }
```
