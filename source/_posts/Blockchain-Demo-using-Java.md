---
title: Blockchain Demo using Java
date: 2017-04-15 15:34:33
categories: BlockChain
tags: [BlockChain, Java, demo]
---

This blog will be removed since some of the concepts are wrong.

<!--more-->

# Flow

## New Blockchain()

1. create an empty chain
2. create genesis block
	1. with null previous hash, current timestamp and null transaction
	2. calculateHash using SHA256;
3. add genesis block into chain
4. define miningReward
5. create an account list and add system as 1st account

```
	public Blockchain() {
		chain = new ArrayList<>();
		genesisBlock = createGenesisBlock();
		chain.add(genesisBlock);
		difficulty = 1;
		pendingTransactions = new ArrayList<>();
		miningReward = 1000;
		bonusAmount = 100;
		accounts = new ArrayList<>();
		accounts.add(new Account("system", "system", 0));
	}
```


## Create account

1. user input any string
2. encript (input+dateTime) to get address and key for a single user
3. add to account list

```
	private void createAccount() {
		System.out.print("Enter your name : ");
		String input = scanner.nextLine();
		Date dateTime = new Date();
		Encrypt encrypt = new Encrypt();
		String address = encrypt.SHA256((input+dateTime)).substring(0, 8);
		String key = encrypt.SHA256((input+dateTime)).substring(8, 16);
		Account newAccount = new Account(address, key, 0);
		blockchain.addAccount(newAccount);
		System.out.println("Account created..");
		System.out.println("\tAddress : " + address);
		System.out.println("\tkey : " + key);
		System.out.println("");
	}
```

## Transaction

1. provide fromAddress, fromKey, toAddress and amountStr
2. check if two addresses are existed and fromKey matches
3. push this transaction to pendingTransactions
4. transaction will only take effects after mining (new block is added into chain)

```
	void createTransaction(Transaction newTransaction, String fromKey) {
		// verification
		boolean isValidFromAddress = false;
		boolean isValidFromKey = false;
		boolean isValidToAddress = false;

		String fromAddress = newTransaction.getFromAddress();
		String toAddress = newTransaction.getToAddress();

		for (Account account : accounts) {
			if (account.getAddress().equals(fromAddress)) {
				isValidFromAddress = true;
				if (account.getKey().equals(fromKey)) {
					isValidFromKey = true;
				}
				break;
			}
		}

		for (Account account : accounts) {
			if (account.getAddress().equals(toAddress)) {
				isValidToAddress = true;
				break;
			}
		}

		if (isValidToAddress && isValidFromAddress && isValidFromKey) {
			pendingTransactions.add(newTransaction);
		} else {
			System.out.println("Invalid fromAddress, fromKey or toAddress..");
		}
	}
```

## Mine Pending Transaction

1. provide miner address
2. new Block using lastest block hash, current timestamp and pendingTransactions
3. mine block by passing difficulty (proof of work)
	1. by passing increasing nonce to get different hash
	2. until 1st two charactars are "00" if difficulty is 2
	```
	void mineBlock(int difficulty) {
		StringBuilder sb = new StringBuilder();
		for (int i = 0; i < difficulty; i++) {
			sb.append("0");
		}
		while (!this.hash.substring(0, difficulty).equals(sb.toString())) {
			this.nonce++;
			this.hash = this.calculateHash();
			System.out.println("hash : " + hash);
		}
	}
	```
4. add new block into the chain
5. close pending transaction
	1. withdraw from fromAddress
	2. top up to toAddress
6. cleare pending transaction
7. give miner reward in the next block
	1. top up system by reward amount
	2. system transfer to miner
	3. transaction push into pending transaction

```
void minePendingTransaction(String miningRewardAddress) {
		Block newBlock = new Block(getLatestBlock().getHash(), new Timestamp(System.currentTimeMillis()), pendingTransactions);
		newBlock.mineBlock(difficulty);
		chain.add(newBlock);

		// close pending transaction
		for (Transaction transaction : pendingTransactions) {
			// deduct from FromAddress
			boolean isWithdrawSuccess = false;
			boolean isTopUpSuccess = false;
			for (Account account : accounts) {
				if (account.getAddress().equals(transaction.getFromAddress())) {
					isWithdrawSuccess = account.withdraw(transaction.getAmount());
					break;
				}
			}
			// top up on toAddress
			for (Account account : accounts) {
				if (isWithdrawSuccess) {
					if (account.getAddress().equals(transaction.getToAddress())) {
						isTopUpSuccess = account.topUp(transaction.getAmount());
						break;
					}
				}
			}

			if (isWithdrawSuccess && !isTopUpSuccess) {
				System.out.println("miningRewardAddress to [" + miningRewardAddress + "] is not successful..");
			}
		}

		// cleare pending transaction
		pendingTransactions = new ArrayList<>();

		// reward given in next new block
		accounts.get(0).topUp(miningReward);
		pendingTransactions.add(new Transaction("system", miningRewardAddress, miningReward));
	}
```

# Code

## RyanCoin
```
package io.ryanluoxu.blockchain;

import java.util.Date;
import java.util.Scanner;

public class RyanCoin {

	static String option = "";
	static Scanner scanner = new Scanner(System.in);
	static Blockchain blockchain;

	public static void main(String[] args) {

		RyanCoin ryanCoin = new RyanCoin();
		blockchain = new Blockchain();

		while (!option.equals("0")) {
			ryanCoin.printMenu();
			System.out.println("");
			System.out.print("    Please choose : ");
			option = ryanCoin.getOption();
			System.out.println("");
			switch (option) {
			case "1":
				ryanCoin.createAccount();
				break;
			case "2":
				ryanCoin.addTransaction();
				break;
			case "3":
				ryanCoin.minePendingTransaction();
				break;
			case "4":
				ryanCoin.viewAccount();
				break;
			case "9":
				ryanCoin.bonus();
				break;

			default:
				System.out.println("Invalid option..");
				break;
			}
		}
	}

	private void viewAccount() {
		blockchain.viewAccount();
	}

	private void bonus() {
		blockchain.bonus();
	}

	private void minePendingTransaction() {
		System.out.print("Miner address : ");
		String miningRewardAddress = scanner.nextLine();
		blockchain.minePendingTransaction(miningRewardAddress);
	}

	private void addTransaction() {
		String fromAddress;
		String fromKey;
		String toAddress;
		String amountStr;

		System.out.print("From Address : ");
		fromAddress = scanner.nextLine();
		System.out.print("From Key : ");
		fromKey = scanner.nextLine();
		System.out.print("To Address : ");
		toAddress = scanner.nextLine();
		System.out.print("Transaction Amount : ");
		amountStr = scanner.nextLine();

		try {
			double amount = Integer.valueOf(amountStr);
			if (amount >= 0) {
				blockchain.createTransaction(new Transaction(fromAddress, toAddress, amount), fromKey);
			} else {
				System.out.println("Invalid amount..");
			}
		} catch (Exception e) {
			System.out.println(e);
			System.out.println("invalid amount..");
		}
	}

	private void createAccount() {
		System.out.print("Enter your name : ");
		String input = scanner.nextLine();
		Date dateTime = new Date();
		Encrypt encrypt = new Encrypt();
		String address = encrypt.SHA256((input+dateTime)).substring(0, 8);
		String key = encrypt.SHA256((input+dateTime)).substring(8, 16);
		Account newAccount = new Account(address, key, 0);
		blockchain.addAccount(newAccount);
		System.out.println("Account created..");
		System.out.println("\tAddress : " + address);
		System.out.println("\tkey : " + key);
		System.out.println("");
	}

	private String getOption() {
		String result = null;
		result = scanner.nextLine();
		return result;
	}

	private void printMenu() {
		System.out.println("");
		System.out.println("\t Ryan Coin 1.0           ");
		System.out.println("\t===============          ");
		System.out.println("\t1. Create Account");
		System.out.println("\t2. New Transaction");
		System.out.println("\t3. Mine Pending Transaction");
		System.out.println("\t4. View Account");
		System.out.println("\t9. Ryan Bonus");
		System.out.println("\t0. Destroy coin");

	}

}
```

## Blockchain
```
package io.ryanluoxu.blockchain;

import java.sql.Timestamp;
import java.util.ArrayList;
import java.util.List;

public class Blockchain {

	private List<Block> chain;
	private Block genesisBlock;
	private int difficulty;
	private List<Transaction> pendingTransactions;
	private long miningReward;
	private long bonusAmount;
	private List<Account> accounts;

	public Blockchain() {
		chain = new ArrayList<>();
		genesisBlock = createGenesisBlock();
		chain.add(genesisBlock);
		// genesisBlock info
		System.out.println("Genesis Block is created:");
		System.out.println("\tgenesisBlock.hash : " + genesisBlock.getHash());
		System.out.println("\tgenesisBlock.nonce : " + genesisBlock.getNonce());
		System.out.println("\tgenesisBlock.timestamp : " + genesisBlock.getTimestamp());
		System.out.println("");

		difficulty = 1;
		pendingTransactions = new ArrayList<>();
		miningReward = 1000;
		bonusAmount = 100;
		accounts = new ArrayList<>();
		accounts.add(new Account("system", "system", 0));
	}

	private Block createGenesisBlock() {
		return new Block(null, new Timestamp(System.currentTimeMillis()), null);
	}

	private Block getLatestBlock(){
		return chain.get(chain.size()-1);
	}

	public boolean isChainValid() {

		for (int i = 0; i < chain.size(); i++) {
			Block currentBlock = chain.get(i);
			Block previousBlock = chain.get(i-1);

			if (currentBlock.getHash() != currentBlock.calculateHash()) {
				return false;
			}

			if (currentBlock.getPreviousHash() != previousBlock.getHash()) {
				return false;
			}
		}

		return true;
	}

	void createTransaction(Transaction newTransaction, String fromKey) {
		// verification
		boolean isValidFromAddress = false;
		boolean isValidFromKey = false;
		boolean isValidToAddress = false;

		String fromAddress = newTransaction.getFromAddress();
		String toAddress = newTransaction.getToAddress();

		for (Account account : accounts) {
			if (account.getAddress().equals(fromAddress)) {
				isValidFromAddress = true;
				if (account.getKey().equals(fromKey)) {
					isValidFromKey = true;
				}
				break;
			}
		}

		for (Account account : accounts) {
			if (account.getAddress().equals(toAddress)) {
				isValidToAddress = true;
				break;
			}
		}

		if (isValidToAddress && isValidFromAddress && isValidFromKey) {
			pendingTransactions.add(newTransaction);
		} else {
			System.out.println("Invalid fromAddress, fromKey or toAddress..");
		}
	}

	void minePendingTransaction(String miningRewardAddress) {
		Block newBlock = new Block(getLatestBlock().getHash(), new Timestamp(System.currentTimeMillis()), pendingTransactions);
		newBlock.mineBlock(difficulty);
		chain.add(newBlock);
		// newBlock info
		System.out.println("new Block is created:");
		System.out.println("\tnewBlock.previousHash : " + newBlock.getPreviousHash());
		System.out.println("\tnewBlock.hash : " + newBlock.getHash());
		System.out.println("\tnewBlock.nonce : " + newBlock.getNonce());
		System.out.println("\tnewBlock.timestamp : " + newBlock.getTimestamp());
		System.out.println("");

		// close pending transaction
		for (Transaction transaction : pendingTransactions) {
			// deduct from FromAddress
			boolean isWithdrawSuccess = false;
			boolean isTopUpSuccess = false;
			for (Account account : accounts) {
				if (account.getAddress().equals(transaction.getFromAddress())) {
					isWithdrawSuccess = account.withdraw(transaction.getAmount());
					break;
				}
			}
			// top up on toAddress
			for (Account account : accounts) {
				if (isWithdrawSuccess) {
					if (account.getAddress().equals(transaction.getToAddress())) {
						isTopUpSuccess = account.topUp(transaction.getAmount());
						break;
					}
				}
			}

			if (isWithdrawSuccess && !isTopUpSuccess) {
				System.out.println("miningRewardAddress to [" + miningRewardAddress + "] is not successful..");
			}
		}

		// cleare pending transaction
		pendingTransactions = new ArrayList<>();

		// reward given in next new block
		accounts.get(0).topUp(miningReward);
//		System.out.println("System top up mining reward amount : " + miningReward);
		pendingTransactions.add(new Transaction("system", miningRewardAddress, miningReward));
	}

	public void addAccount(Account newAccount) {
		accounts.add(newAccount);
	}

	public void bonus() {
		for (int i = 1; i < accounts.size(); i++) {
			accounts.get(0).topUp(bonusAmount);
//			System.out.println("System top up bonus amount : " + bonusAmount);
			pendingTransactions.add(new Transaction("system", accounts.get(i).getAddress(), bonusAmount));
		}
	}

	public void viewAccount() {
		System.out.println("View Accounts: ");
		for (int i = 1; i < accounts.size(); i++) {
			System.out.println("\t" + accounts.get(i).getAddress() + " : " + accounts.get(i).getBalance());
		}
		System.out.println("");
	}
}
```

## Block

```
package io.ryanluoxu.blockchain;

import java.sql.Timestamp;
import java.util.List;

public class Block {

	private long nonce;
	private String previousHash;
	private Timestamp timestamp;
	private List<Transaction> transactions;
	private String hash;

	public Block(String previousHash, Timestamp timestamp, List<Transaction> transactions) {
		this.previousHash = previousHash;
		this.timestamp = timestamp;
		this.transactions = transactions;
		this.hash = calculateHash();
		this.nonce = 0;
	}

	String calculateHash() {
		Encrypt encrypt = new Encrypt();
		String strText = null;
		if (previousHash == null) {
			if (transactions == null) {
				strText = ("" + timestamp + "" + nonce).toString();
			} else {
				strText = ("" + timestamp + transactions.toString() + nonce).toString();
			}
		} else {
			strText = (previousHash + timestamp + transactions.toString() + nonce).toString();
		}
//		System.out.println("strText of block : "+strText);
		return encrypt.SHA256(strText);
	}

	void mineBlock(int difficulty) {
		StringBuilder sb = new StringBuilder();
		for (int i = 0; i < difficulty; i++) {
			sb.append("0");
		}
		while (!this.hash.substring(0, difficulty).equals(sb.toString())) {
			this.nonce++;
			this.hash = this.calculateHash();
			System.out.println("hash : " + hash);
		}
	}

	public long getNonce() {
		return nonce;
	}

	public String getPreviousHash() {
		return previousHash;
	}

	public Timestamp getTimestamp() {
		return timestamp;
	}

	public List<Transaction> getTransactions() {
		return transactions;
	}

	public String getHash() {
		return hash;
	}

}

```

## Encrypt

```
package io.ryanluoxu.blockchain;

import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

public class Encrypt {

	public String SHA256(final String strText) {
		return SHA(strText,"SHA-256");
	}

	public String SHA512(final String strText) {
		return SHA(strText,"SHA-512");
	}

	private String SHA(final String strText, final String strType) {
		String strResult = null;

		if (strText != null && strText.length() > 0) {

			byte byteBuffer[] = null;

			try {
				MessageDigest messageDigest = MessageDigest.getInstance(strType);
				messageDigest.update(strText.getBytes());
				byteBuffer = messageDigest.digest();
			} catch (NoSuchAlgorithmException e) {
				e.printStackTrace();
			}

			StringBuffer strHexString = new StringBuffer();
			if (byteBuffer != null && byteBuffer.length > 0) {
				for (int i = 0; i < byteBuffer.length; i++) {
					String hex = Integer.toHexString(0xff & byteBuffer[i]);
					if (hex.length() == 1) {
						strHexString.append('0');
					}
					strHexString.append(hex);
				}
				strResult = strHexString.toString();
			} else {
				System.out.println("messageDigest.digest() fail..");
			}
		}
		return strResult;
	}

}
```

## Transaction

```
package io.ryanluoxu.blockchain;

public class Transaction {

	private String fromAddress;
	private String toAddress;
	private double amount;

	public Transaction(String fromAddress, String toAddress, double amount) {
		super();
		this.fromAddress = fromAddress;
		this.toAddress = toAddress;
		this.amount = amount;
	}

	public String getFromAddress() {
		return fromAddress;
	}

	public void setFromAddress(String fromAddress) {
		this.fromAddress = fromAddress;
	}

	public String getToAddress() {
		return toAddress;
	}

	public void setToAddress(String toAddress) {
		this.toAddress = toAddress;
	}

	public double getAmount() {
		return amount;
	}

	public void setAmount(double amount) {
		this.amount = amount;
	}

}

```

## Account

```
package io.ryanluoxu.blockchain;

public class Account {

	private String address;
	private String key;
	private double balance;
	public Account(String address, String key, double balance) {
		this.address = address;
		this.key = key;
		this.balance = balance;
	}
	public String getAddress() {
		return address;
	}
	public String getKey() {
		return key;
	}
	public double getBalance() {
		return balance;
	}
	public boolean topUp(double amount) {
		balance += amount;
		System.out.println("Top up of [" + amount + "] into [" + address + "] is successful..");
		return true;
	}

	public boolean withdraw(double amount) {
		if (balance - amount >= 0) {
			balance -= amount;
			System.out.println("Withdraw of [" + amount + "] from [" + address + "] is successful..");
			return true;
		}
		System.out.println("Insuffient balance..");
		return false;
	}
}

```
