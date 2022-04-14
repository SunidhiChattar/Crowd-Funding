//SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.5.0<0.9.0;

contract CrowdFunding{
    mapping(address=>uint) public contributors; //which contributor is donating how much amount
    address public manager; //define each variable used in the smart contract
    uint public minimumcontribution;
    uint public deadline;
    uint public target;
    uint public raisedAmount;
    uint public noOfContributors;

    struct Request{ //why to raise the fund,specify the name of the charity,address and how much fund needed
        string  description;
        address payable recipient;
        uint value;
        bool completed;
        uint noOfVoters;
        mapping(address=>bool) voters;


    }
    mapping(uint=>Request) public requests;
    uint public numRequests;//no. of requests

    constructor(uint _target, uint _deadline){
        target=_target; //target and deadline would be set
        deadline= block.timestamp+_deadline;
        minimumcontribution=100 wei;
        manager=msg.sender; //manager will recive the info about target and deadline

    }
    function sendWEI() public payable{//contributors contributes money
        require(block.timestamp<=deadline,"Deadline has passed");
        require(msg.value>=minimumcontribution,"Minimum Contribution is not met");

        if(contributors[msg.sender]==0)
        {
            noOfContributors++;

        }
        contributors[msg.sender]+=msg.value;
        raisedAmount+=msg.value;


    }
    function getContractBal() public view returns(uint){
        return address(this).balance; //current available balance
    }
    function refund() public {//if deadline reaches and target is not fullfiled then money will be refunded
        require(block.timestamp>deadline && raisedAmount<target,"You are not eligible");
        require(contributors[msg.sender]>0);
        address payable user=payable(msg.sender);
        user.transfer(contributors[msg.sender]);
        contributors[msg.sender]=0;

    }
    modifier onlyManager(){//assigning only to manager
        require(msg.sender==manager,"Only manager can call the function");
        _;

    }
    //if we make a structure and inside of it mapping is done and then if it is used in function then storage keyword shall be used or else error occurs
    function createRequests(string memory _description, address payable _recipient ,uint _value ) public onlyManager{
        Request storage newRequest = requests[numRequests];
        numRequests++;
        newRequest.description=_description;
        newRequest.recipient=_recipient;
        newRequest.value=_value;
        newRequest.completed=false;
        newRequest.noOfVoters=0;
    }
    function voteRequest(uint _requestNo) public {//only contributors can vote
        require(contributors[msg.sender]>0, "You must be contributor");
        Request storage thisRequest=requests[_requestNo];
        require(thisRequest.voters[msg.sender]==false,"You have already voted");
        thisRequest.voters[msg.sender]==true;
        thisRequest.noOfVoters++;

    }
    function makePayment(uint _requestNo) public onlyManager{//if noOfVoter>noOfContributors/2 then makepayment works
        require(raisedAmount >=target);
        Request storage thisRequest=requests[_requestNo];
        require(thisRequest.completed==false,"The has been completed");
        require(thisRequest.noOfVoters> noOfContributors/2,"Majority of voters not agree");
        thisRequest.recipient.transfer(thisRequest.value);
        thisRequest.completed=true;
       
    }
}
