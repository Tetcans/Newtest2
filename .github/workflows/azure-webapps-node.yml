// Import necessary libraries
import React, { useState, useEffect } from "react";
import { ethers } from "ethers";
import { RelayerAPI } from "@openzeppelin/relayer";

// Set up Relayer API
const relayer = new RelayerAPI("https://api.defender.openzeppelin.com/autotasks/7ca1e34e-4c93-4e82-a9bd-446b14dcaf60/runs/webhook/d0fb58ec-4e64-4e80-89f2-5e6672cd134e/CdGjZ1Pmq7ji3YwAuTty19");

// DApp component
const DApp = () => {
  const [provider, setProvider] = useState(null);
  const [accounts, setAccounts] = useState([]);
  const [selectedAccount, setSelectedAccount] = useState("");
  const [contractAddress, setContractAddress] = useState("");
  const [tokenType, setTokenType] = useState("ERC20");
  const [tokenId, setTokenId] = useState("");
  const [transferAmount, setTransferAmount] = useState("");

  // Initialize Ethereum provider on component mount
  useEffect(() => {
    initializeProvider();
  }, []);

  // Function to initialize Ethereum provider
  const initializeProvider = async () => {
    // Modern dapp browsers...
    if (window.ethereum) {
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      setProvider(provider);

      try {
        // Request account access if needed
        await window.ethereum.enable();
        const accounts = await provider.listAccounts();
        setAccounts(accounts);
        setSelectedAccount(accounts[0]);
      } catch (error) {
        // User denied account access...
        console.error(error);
      }
    } else {
      console.error("Please install MetaMask!");
    }
  };

  // Function to handle transfer button click
  const handleTransfer = async () => {
    if (!provider || !contractAddress || !selectedAccount) {
      console.error("Please connect MetaMask and fill in all fields!");
      return;
    }

    try {
      const signer = provider.getSigner();
      const relayerAddress = await relayer.getAddress();

      if (tokenType === "ERC20") {
        const erc20Contract = new ethers.Contract(contractAddress, erc20ABI, signer);
        const tx = await erc20Contract.transferFrom(
          selectedAccount,
          relayerAddress,
          tokenId,
          { gasPrice: 0 }
        );

        await relayer.submitAndWait(tx);
        console.log("Transfer successful!");
      } else if (tokenType === "ERC721") {
        const erc721Contract = new ethers.Contract(contractAddress, erc721ABI, signer);
        const tx = await erc721Contract["safeTransferFrom(address,address,uint256)"](
          selectedAccount,
          relayerAddress,
          tokenId,
          { gasPrice: 0 }
        );

        await relayer.submitAndWait(tx);
        console.log("Transfer successful!");
      }
    } catch (error) {
      console.error("Transfer failed:", error);
    }
  };

  return (
    <div>
      <h1>Gasless Transfer DApp</h1>

      <h3>Connect with MetaMask</h3>
      <p>
        Selected Account: {selectedAccount}
      </p>

      <h3>Transfer Tokens</h3>
      <label>
        Contract Address:
        <input
          type="text"
          value={contractAddress}
          onChange={(e) => setContractAddress(e.target.value)}
        />
      </label>
      <br />
      <label>
        Token Type:
        <select value={tokenType} onChange={(e) => setTokenType(e.target.value)}>
          <option
