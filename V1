// SPDX-Licence-Identifier : MIT

pragma solidity 0.8.16;

contract MySmartWallet {

    address payable public owner;
    address payable nextOwner;
    mapping (address => uint )public  allowance;
    mapping (address => bool ) public isAllowedtoSend;
    mapping (address => bool) public guardians;
    mapping (address => mapping(address => bool)) nextOwnerGuardianVotedBool;
    uint minimumNumberOfGuardians = 3;
    uint public numberOfGuardiansThatAgree = 0 ;


    constructor() {
        owner = payable(msg.sender);
    }


    receive() external payable {}

    function GetBalance() public view returns (uint) {
        return address(this).balance;
    }


    function setAllowance (address _to, uint _amount) public {
        require(msg.sender == owner, "You are not an owner, aborting !");
        allowance[_to] = _amount;
        isAllowedtoSend[_to] = true;
    }

    function setGuardians(address _to) public {
        require(msg.sender == owner, "you can't add guardians, aborting");
        guardians[_to] = true;

    }

    function changeOwner (address _newOwner) public {
        require(guardians[msg.sender], "you can't change the Owner, aborting") ;
        require(nextOwnerGuardianVotedBool[_newOwner][msg.sender] == false, "you have already voted for this new Owner");
        if(_newOwner != nextOwner) {
            nextOwner =payable( _newOwner);
            numberOfGuardiansThatAgree = 0;
        }
        numberOfGuardiansThatAgree ++;
        nextOwnerGuardianVotedBool[_newOwner][msg.sender] = true;
        if(numberOfGuardiansThatAgree >= minimumNumberOfGuardians) {
            owner = payable(_newOwner);
            nextOwner = payable (address(0));
        }

    }


    function transfer (address _to, uint _amount, bytes memory _payload) public returns (bytes memory) {
        require(_amount <= address(this).balance, "you are trying to spend more that you the contract owns");
        if (msg.sender != owner) {
            require (isAllowedtoSend[msg.sender] == true,"you can't send any money");
            require(allowance[msg.sender] >= _amount, "you are trying to spend more than you are allowed to");
            allowance[msg.sender] -= _amount;
        }

        (bool success, bytes memory returnData) = _to.call{value : _amount}(_payload);
        require(success, "transaction failed");
        return returnData;

    }

    function transferToEOA (address _to,uint _amount) public {
        require(_amount <= address(this).balance, "you are trying to spend more that you the contract owns");
        if (msg.sender != owner) {
            require (isAllowedtoSend[msg.sender] == true,"you can't send any money");
            require(allowance[msg.sender] >= _amount, "you are trying to spend more than you are allowed to");
            allowance[msg.sender] -= _amount;
        }

        payable(_to).transfer(_amount);
    }


}


contract InteractWithMyWallet {

    
    function GetBalance() public view returns (uint) {
        return address(this).balance;
    }



}
