## Intro to Blockchain with Ethereum, Web3j and Spring Boot: Smart Contracts  [![Twitter](https://img.shields.io/twitter/follow/piotr_minkowski.svg?style=social&logo=twitter&label=Follow%20Me)](https://twitter.com/piotr_minkowski)

Detailed description can be found here: [Intro to Blockchain with Ethereum, Web3j and Spring Boot: Smart Contracts](https://piotrminkowski.com/2018/07/25/intro-to-blockchain-with-ethereum-web3j-and-spring-boot-smart-contracts/) 


Smart Wallet

Bitcoin Core Created by Satoshi Nakamoto for Bitcoin, it is also called the Satoshi client (https://bitcoin.org/en/bitcoin-core/).
Ethereum Ethereum is an open-source, public, blockchain-based distributed computing platform, which offers a cryptocurrency called Ether (https://www.ethereum.org/).
Ripple Ripple is a real-time payment system, based on a shared public XRP Ledger. It has been used by banks such as UniCredit, UBS, and Santander (https://ripple.com/).
Bitcoin Cash Bitcoin Cash is a hard fork of Bitcoin, only started in 2017. Bitcoin Cash claims to be faster and cheaper to use, with the maximum block size of 8MB (https://www.bitcoincash.org/).
Electrum Electrum is a lightweight Bitcoin client, which comes with both a hardware wallet and a software wallet. Hardware wallets allow users to store the Bitcoin information such as private keys in a hardware device, such as a USB memory stick. Unlike a software wallet, a hardware wallet can be physically secured and is immune from viruses, which are designed to steal from software wallets. (https://electrum.org/).
Coinbase Coinbase claims to be one of the most well-known and trusted apps to buy, sell, and manage your digital currency (https://www.coinbase.com/).
Blockchain Luxembourg This platform has a beautiful user interface that's easy to use and many useful features for cryptocurrency (https://www.blockchain.com/).
Figure 10.8 shows more cryptocurrency wallets from the bitcoin.org web site (https://bitcoin.org/en/choose-your-wallet). Figure 10.9 shows the Live Coin Watch web site, where you can find more details, such as price, market capitalization, volume, and trend, of each cryptocurrency (https://www.livecoinwatch.com/). According to Live Coin Watch, the top three most popular cryptocurrencies are BTC (Bitcoin), XRP (Ripple), and ETH (Ethereum), according to their Market Cap (market capitalization; that is, the total dollar market value). BTC is about $70B, XRP is about $15B, and ETH is about $12B, far ahead of the other cryptocurrencies.

https://bitcoin.org/bitcoin.pdf

https://en.bitcoin.it/wiki/Main_Page

https://bitcoin.org/en/


//Example 10.1A Block.java
import java.security.*;
import java.util.*;
 
public class Block {
    public int index;
    public long timestamp;
    public String currentHash;
    public String previousHash;
    public String data;
    public int nonce;
    
    public Block(int index, String previousHash, String data) {
        this.index = index;
        this.timestamp = System.currentTimeMillis();
        this.previousHash = previousHash;
        this.data = data;
        nonce = 0;
        currentHash = calculateHash();
    }                    

    public String calculateHash(){
        try {
            String input = index + timestamp + previousHash + data + nonce;
            MessageDigest digest = MessageDigest.getInstance("SHA-256");
            byte[] hash = digest.digest(input.getBytes("UTF-8"));
            
            StringBuffer hexString = new StringBuffer(); 
            for (int i = 0; i < hash.length; i++) {
                String hex = Integer.toHexString(0xff & hash[i]);
                if(hex.length() == 1) hexString.append('0');
                hexString.append(hex);
            }
            return hexString.toString();
        }
        catch(Exception e) {
            throw new RuntimeException(e);
        }
    }
    public void mineBlock(int difficulty) {
        nonce = 0;
        String target = new String(new char[difficulty]).replace('\0', '0'); 
        while (!currentHash.substring(0,  difficulty).equals(target)) {
            nonce++;
            currentHash = calculateHash();
        }
    }
    public String toString() {
        String s = "Block #      : " + index + "\r\n";
        s = s +    "PreviousHash : " + previousHash + "\r\n"; 
        s = s +    "Timestamp    : " + timestamp + "\r\n";
        s = s +    "Data         : " + data + "\r\n"; 
        s = s +    "Nonce        : " + nonce + "\r\n"; 
        s = s +    "CurrentHash  : " +currentHash + "\r\n";
    return s;
    }
}
//Example 10.1B BlockChainMain.java
import java.util.*;
 
public class BlockChainMain {

    
    public static ArrayList<Block> blockchain = new ArrayList<Block>();
    public static int difficulty = 5;
 
    public static void main(String[] args) {    
        Block b = new Block(0, null, "My First Block");  //The genesis block
        b.mineBlock(difficulty);
        blockchain.add(b);
        System.out.println(b.toString());
 
        Block b2 = new Block(1, b.currentHash, "My Second Block");
        b2.mineBlock(difficulty);
        blockchain.add(b2);
        System.out.println(b2.toString());
    }
}
//Example 10.1C BlockChainMain2.java
import java.util.*;
 
public class BlockChainMain2 {
    
    public static ArrayList<Block> blockchain = new ArrayList<Block>();
    public static int difficulty = 5;
 
    public static void main(String[] args) {    
        Block b = new Block(0, null, "My First Block");
        b.mineBlock(difficulty);
        blockchain.add(b);
        System.out.println(b.toString());
        System.out.println("Current Block Valid: " + validateBlock(b, null));
 
        Block b2 = new Block(1, b.currentHash, "My Second Block");
        b2.mineBlock(difficulty);
        blockchain.add(b2);
        //b2.data="My Third Block";
        System.out.println(b2.toString());
        System.out.println("Current Block Valid: " + validateBlock(b2, b));
    }
    public static boolean validateBlock(Block newBlock, Block previousBlock) {
        
        if (previousBlock == null){  //The first block
            if (newBlock.index != 0) {
              return false;
            }
 
            if (newBlock.previousHash != null) {
              return false;
            }
 
            if (newBlock.currentHash == null || 
                  !newBlock.calculateHash().equals(newBlock.currentHash)) {
              return false;
            }
 
            return true;
 
        } else{                        //The rest blocks
            if (newBlock != null ) {
              if (previousBlock.index + 1 != newBlock.index) {
                return false;
              }
 
              if (newBlock.previousHash == null  ||  
                !newBlock.previousHash.equals(previousBlock.currentHash)) {
                return false;
              }
 
              if (newBlock.currentHash == null  ||  
                !newBlock.calculateHash().equals(newBlock.currentHash)) {
                return false;

              }
 
              return true;
            }
            return false;
           
        }
    }
}
//Example 10.1D BlockChainMain3.java
import java.util.*;
 
public class BlockChainMain3 {
    
    public static ArrayList<Block> blockchain = new ArrayList<Block>();
    public static int difficulty = 5;
 
    public static void main(String[] args) {    
        Block b = new Block(0, null, "My First Block");
        b.mineBlock(difficulty);
        blockchain.add(b);
        System.out.println(b.toString());
 
        Block b2 = new Block(1, b.currentHash, "My Second Block");
        b2.mineBlock(difficulty);
        blockchain.add(b2);
        System.out.println(b2.toString());
        System.out.println("Current Chain Valid: " +validateChain(blockchain));
    }
    public static boolean validateChain(ArrayList<Block> blockchain) {
        if (!validateBlock(blockchain.get(0), null)) {
          return false;
        }
 
        for (int i = 1; i < blockchain.size(); i++) {
          Block currentBlock = blockchain.get(i);
          Block previousBlock = blockchain.get(i - 1);
 
          if (!validateBlock(currentBlock, previousBlock)) {
            return false;
          }
        }
 
        return true;
    }
    public static boolean validateBlock(Block newBlock, Block previousBlock) {
        //The same code as before
        … …
    }
}
//Example 10.2A Block2.java
public class Block2 {
    public int index;
    public long timestamp;
    public String currentHash;
    public String previousHash;
    public String data;
    public ArrayList<Transaction> transactions = new ArrayList<Transaction>(); //our data will be a simple message.
    public int nonce;
    
    public Block2(int index, String previousHash, ArrayList<Transaction> transactions) {
        this.index = index;
        this.timestamp = System.currentTimeMillis();
        this.previousHash = previousHash;
        this.transactions = transactions;
        nonce = 0;
        currentHash = calculateHash();
    }
 
    public String calculateHash(){
        try {

            data="";
            for (int j=0; j<transactions.size();j++){
                Transaction tr = transactions.get(j);
                data = data + tr.sender+tr.recipient+tr.value;
            }
            String input = index + timestamp + previousHash + data + nonce;
            MessageDigest digest = MessageDigest.getInstance("SHA-256");
            byte[] hash = digest.digest(input.getBytes("UTF-8"));
            
            StringBuffer hexString = new StringBuffer(); 
            for (int i = 0; i < hash.length; i++) {
                String hex = Integer.toHexString(0xff & hash[i]);
                if(hex.length() == 1) hexString.append('0');
                hexString.append(hex);
            }
            return hexString.toString();
        }         catch(Exception e) {
            throw new RuntimeException(e);
        }
    }
 
    public void mineBlock(int difficulty) {
        nonce = 0;
        String target = new String(new char[difficulty]).replace('\0', '0'); 
        while (!currentHash.substring(0,  difficulty).equals(target)) {
            nonce++;
            currentHash = calculateHash();
        }
    }
 
    public String toString() {
        String s = "Block #      : " + index + "\r\n";
        s = s +    "PreviousHash : " + previousHash + "\r\n"; 
        s = s +    "Timestamp    : " + timestamp + "\r\n";
        s = s +    "Transactions  : " + data + "\r\n"; 
        s = s +    "Nonce        : " + nonce + "\r\n"; 
        s = s +    "CurrentHash  : " +currentHash + "\r\n";
    return s;
    }        
}
//Example 10.2B Transaction.java
import java.util.*;
 
public class Transaction {
    public String sender; 
    public String recipient; 
    public float value; 
        
    public Transaction(String from, String to, float value) {
        this.sender = from;
        this.recipient = to;
        this.value = value;
    }
}
//Example 10.2C Wallet.java
import java.security.*;
import java.util.*;
 
public class Wallet {
    
    public String privateKey;
    public String publicKey;
    private float balance=100.0f;
    private ArrayList<Block2> blockchain = new ArrayList<Block2>();
    
    public Wallet(ArrayList<Block2> blockchain) {
        generateKeyPair();
        this.blockchain = blockchain;
    }
        
    public void generateKeyPair() {

        try {
            KeyPair keyPair;
            String algorithm = "RSA"; //DSA DH etc
            keyPair = KeyPairGenerator.getInstance(algorithm).generateKeyPair();
            privateKey = keyPair.getPrivate().toString();
            publicKey = keyPair.getPublic().toString();
            
        }catch(Exception e) {
            throw new RuntimeException(e);
        }
    }
    
    public float getBalance() {
        float total = balance;    
        for (int i=0; i<blockchain.size();i++){
            Block2 currentBlock = blockchain.get(i);
            for (int j=0; j<currentBlock.transactions.size();j++){
                Transaction tr = currentBlock.transactions.get(j);
                if (tr.recipient.equals(publicKey)){
                    total += tr.value;
                }
                if (tr.sender.equals(publicKey)){
                    total -= tr.value;
                }
            }
        }  
        return total;
    }
    
    public Transaction send(String recipient,float value ) {
        if(getBalance() < value) {
            System.out.println("!!!Not Enough funds. Transaction Discarded.");
            return null;
        }
        
        Transaction newTransaction = new Transaction(publicKey, recipient , value);
        return newTransaction;
    }
    
}
//Example 10.2D BlockChainMain4.java
import java.util.*;
 
public class BlockChainMain4 {
    
    public static ArrayList<Block2> blockchain = new ArrayList<Block2>();
    public static ArrayList<Transaction> transactions = new ArrayList<Transaction>();
    public static int difficulty = 5;
 
    public static void main(String[] args) {
        Wallet A = new Wallet(blockchain);
        Wallet B = new Wallet(blockchain);
        System.out.println("Wallet A Balance: " + A.getBalance());
        System.out.println("Wallet B Balance: " + B.getBalance());
        
 
    }
}
//Example 10.2E BlockChainMain5.java
import java.util.*;
 
public class BlockChainMain5 {
    
    public static ArrayList<Block2> blockchain = new ArrayList<Block2>();
    public static ArrayList<Transaction> transactions = new ArrayList<Transaction>();

    public static int difficulty = 5;
 
    public static void main(String[] args) {
        Wallet A = new Wallet(blockchain);
        Wallet B = new Wallet(blockchain);
        System.out.println("Wallet A Balance: " + A.getBalance());
        System.out.println("Wallet B Balance: " + B.getBalance());
        
        System.out.println("Add two transactions… ");
        Transaction tran1 = A.send(B.publicKey, 10);
        if (tran1!=null){
            transactions.add(tran1);
        }
        Transaction tran2 = A.send(B.publicKey, 20);
        if (tran2!=null){
            transactions.add(tran2);
        }
        
        Block2 b = new Block2(0, null, transactions);
        b.mineBlock(difficulty);
        blockchain.add(b);
          
        System.out.println("Wallet A Balance: " + A.getBalance());
        System.out.println("Wallet B Balance: " + B.getBalance());
        System.out.println("Blockchain Valid : " + validateChain(blockchain));
 
    }
    public static boolean validateChain(ArrayList<Block2> blockchain) {
        if (!validateBlock(blockchain.get(0), null)) {
          return false;
        }
 
        for (int i = 1; i < blockchain.size(); i++) {
          Block2 currentBlock = blockchain.get(i);
          Block2 previousBlock = blockchain.get(i - 1);
 
          if (!validateBlock(currentBlock, previousBlock)) {
            return false;
          }
        }
 
        return true;
    }
    public static boolean validateBlock(Block2 newBlock, Block2 previousBlock) {
           //The same as before
    }
}
BitcoinJ is an open source library for developing Java Bitcoin applications. BitcoinJ allows you to maintain a wallet and to send and receive transactions without needing a local copy of Bitcoin Core. To use BitcoinJ, first you will need to download the BitcoinJ JAR file bitcoinj-core-0.14.4-bundled.jar from its web site.

https://bitcoinj.github.io/getting-started-java

Alternatively, you can just directly download the JAR file from the following links:

https://search.maven.org/remotecontent?filepath=org/bitcoinj/bitcoinj-core/0.14.4/bitcoinj-core-0.14.4-bundled.jar

https://jar-download.com/artifacts/org.bitcoinj

Second, you will also need to download the Simple Logging Facade for Java (SLF4J) library from this site:

https://www.slf4j.org/download.html

Extract the downloaded file to a folder, and get a file named something like slf4j-simple-1.7.25.jar. Again, don't worry if your version number is slightly different.

Example 10.3 is a simple demonstration program, modified from the BitcoinJ example DumpWallet.java at this site:

https://github.com/bitcoinj/bitcoinj/blob/master/examples/src/main/java/org/bitcoinj/examples/DumpWallet.java

//Example 10.3 DumpWallet1.java
//Modified from https://github.com/bitcoinj/bitcoinj/blob/master/examples/src/main/java/org/bitcoinj/examples/DumpWallet.java
import java.io.File;
import org.bitcoinj.wallet.Wallet;
/**
 * DumpWallet loads a serialized wallet and prints information about what it contains.
 */
public class DumpWallet1 {
    public static void main(String[] args) throws Exception {
        String walletfile="/path/to/your/walletfile";
        File f=new File(walletfile);
        Wallet wallet = Wallet.loadFromFile(f);
        System.out.println(wallet.toString());
    }
}
javac -classpath ".;bitcoinj-core-0.14.4-bundled.jar;slf4j-simple-1.7.25.jar" DumpWallet1.java
java -classpath ".;bitcoinj-core-0.14.4-bundled.jar;slf4j-simple-1.7.25.jar" DumpWallet1

You will need a wallet file to make this program work. See section 10.10 to learn how to create a digital wallet file. For more BitcoinJ examples, you can download the entire source code from its Github web site here:

https://github.com/bitcoinj/bitcoinj

This site offers a simple tutorial on how to build a simple GUI wallet using BitcoinJ library:

https://bitcoinj.github.io/simple-gui-wallet

10.9.1 THE TESTNET
Before you run your program on real Bitcoin, it is always a good idea to test it in a simulated environment. The Bitcoin community provides a simulated Bitcoin network called the testnet. With the testnet, you can send and receive coins. The coins on the testnet have no value and can be obtained for free from testnet faucet sites like these:

https://testnet-faucet.mempool.co/

http://tpfaucet.appspot.com/ .

For more information about the BitcoinJ library, see the following:

https://bitcoinj.github.io/

https://github.com/bitcoinj/bitcoinj

10.10 Java Web3j Examples
Web3j is a lightweight Java library for integration with Ethereum clients. With Web3j you can create a digital wallet, manage the wallet, send Ether, the Ethereum digital cryptocurrency, and create a smart contract. The easiest way to use the Web3j library is to download its command line tools from the following GitHub site:

https://github.com/web3j/web3j/releases/tag/v4.0.1

There, look for a zip file named web3j-4.0.1.zip. More information about the Web3j command-line tools can be found here:

https://docs.web3j.io/command_line.html

Unzip the web3j-4.0.1.zip file to a folder, in this case E:\web3j-4.0.1\. The main command-line tool program is named web3j.bat in the E:\web3j-4.0.1\bin\ folder. Open an MS-DOS terminal, go to the E:\ folder, and type in the following command to create a digital wallet, as shown in Figure 10.17:



The Web3j command to create a digital wallet (top) and using the type command to display the content of the wallet (bottom)

.\web3j-4.0.1\bin\web3j wallet create
You will need to provide a password to your digital wallet; once you've done that, a wallet file with a filename like the following will be created:

UTC--2018-11-26T13-18-32.250132200Z--ccded263b9310c875d615bf66ba678e 121c26362.json
The default location for the digital wallet is shown next, where %USERPROFILE% is your user folder. For example, on my computer it is C:\Users\xiaop.

%USERPROFILE%\AppData\Roaming\Ethereum\testnet\keystore But you can change it to another folder.

You can use the Windows type command (or any text editor) to display the content of the wallet file, as shown in the following output. The address in the wallet is your unique address, which can be used to send Ether or create a smart contract. The crypto in the wallet specifies the encryption algorithm you are using and your private key.

{
"address":"168d8513597cc0958f635a679a5b60ccd13d6ef1",
"id":"721b533c-c552-4f94-9860-5b70cb45497a",
"version":3,
"crypto":{"cipher":"aes-128-ctr",
"ciphertext":"1924aaf30e9b6ea7360caeeab7f1afac196aa9455fea022186642a6dc35c7cd7",
"cipherparams":{"iv":"70c135529198af334f952192f577f2b6"},
"kdf":"scrypt",
"kdfparams":{"dklen":32,"n":262144,"p":1,"r":8,"salt":"68a357b71dc0f3a92726a761125f9cabee2ed4a1d6d0ea183ee6c37d608c97e9"},
"mac":"6dab6ec9ee482c10556e26264c13047a6d9386f2c1ae4cea1c26e0bc5d662bab"}
}
You can use the Etherscan web site to display the transactions at your address; the URL is as follows:

https://etherscan.io/address/<your address>

Figure 10.18 shows the Etherscan web site of the address specified in the digital wallet (https://etherscan.io/address/0x168d8513597cc0958f635a679a5b60ccd13d6ef1). Because you have just started, there are no transactions at the moment.


he Etherscan web site can show the transactions for your address.

Before you can send anybody any Ether, you will need to get some Ether. You can get Ether either by mining it or by obtaining it from someone else. Once you have some Ether, you can use the following command to send it to someone:

.\web3j-4.0.1\bin\web3j wallet send <walletfile> 0x<address>|<ensName>
It will ask you to enter the wallet password, confirm the receiver address, specify the amount of Ether you want to send, and specify the Ether unit (such as ether or wei).

For more details on smart contracts and Web3j examples, see the following web sites:

https://docs.web3j.io/smart_contracts.html

https://github.com/web3j/sample-project-gradle

https://github.com/web3j/examples

The following are Web3j official web sites:

https://web3j.io/

https://github.com/web3j/web3j

https://docs.web3j.io/

10.11 Java EthereumJ Examples
EthereumJ is a pure-Java implementation of the Ethereum protocol. The EthereumJ library allows you to interact with the Ethereum blockchain using Java. The easiest way to obtain and use EthereumJ is by using Git; see Appendix C for more details on how to download, install, and use Git.

In Windows, just run the Git Bash program, and from the Git Bash command line, type the following command to download and run a simple EthereumJ starter example:

git clone https://github.com/ether-camp/ethereumj.starter
cd ethereumj.starter
./gradlew run 
Please note that the ./gradlew run command will download quite a few files and may take several minutes. It will also configure and run a local REST server. To check the results, type the following command at the Git Bash command line, to view the information for the local blockchain:

curl -w "\n" -X GET http://localhost:8080/bestBlock 
The following command will download, or clone, the entire EthereumJ project source:

git clone https://github.com/ethereum/ethereumj
After the download, you can change to the ethereumj subfolder and run an example program named TestNetSample, which is an example for Testnet (see section 10.9.1). Also shown here are the truncated results (the full results are too long):

cd ethereumj
./gradlew run -PmainClass=org.ethereum.samples.TestNetSample
Starting a Gradle Daemon, 1 incompatible Daemon could not be reused, use --status for details
Building version: 1.13.0-SNAPSHOT (from branch develop)
publishing if master || develop current branch: null
[buildinfo] Properties file path was not found! (Relevant only for builds running on a CI Server)
 
:ethereumj-core:processResources
This will be printed after the build task even if something else calls the build task
:ethereumj-core:classes
:ethereumj-core:run
20:09:17.397 INFO [sample]  Starting EthereumJ!
20:09:17.444 INFO [general]  Starting EthereumJ…
20:09:26.092 INFO [general]  External address identified: 59.72.70.14
20:09:26.170 INFO [discover]  Pinging discovery nodes…
 
20:09:26.339 INFO [general]  EthereumJ node started: enode://23b940843d8adf1fb0ccfe4a781e9e35850be7f3aaf6dd5e3b1f2371c1361cc2baaac2d8c95882c34b7ae5ff18bf8a504d50e99f22ad4ee60963fa4930d0c9e8@59.72.70.14:30303
20:09:26.355 INFO [general]  DB is empty - adding Genesis
20:09:26.424 INFO [general]  Genesis block loaded
20:09:26.439 INFO [ethash]  Kept caches: cnt: 1 epochs: 0…0
20:09:26.471 INFO [general]  Bind address wasn't set, Punching to identify it…
20:09:47.519 WARN [general]  Can't get bind IP. Fall back to 0.0.0.0: java.net.ConnectException: Connection timed out: connect
20:09:47.519 INFO [discover]  Discovery UDPListener started
20:09:47.688 INFO [net]  Listening for incoming connections, port: [30303]
20:09:47.688 INFO [net]  NodeId: [23b940843d8adf1fb0ccfe4a781e9e35850be7f3aaf6dd5e3b1f2371c1361cc2baaac2d8c95882c34b7ae5ff18bf8a504d50e99f22ad4ee60963fa4930d0c9e8]
20:09:47.741 INFO [discover]  Reading Node statistics from DB: 0 nodes.
20:09:48.085 INFO [discover]  Received response.
20:09:49.001 INFO [discover]  New peers discovered.
20:09:57.570 INFO [net]  TCP: Speed in/out 3Kb / 3Kb(sec), packets in/out 91/150, total in/out: 31Kb / 37Kb
20:09:57.570 INFO [net]  UDP: Speed in/out 8Kb / 6Kb(sec), packets in/out 438/465, total in/out: 81Kb / 63Kb
20:10:07.575 INFO [net]  TCP: Speed in/out 3Kb / 4Kb(sec), packets in/out 98/162, total in/out: 66Kb / 78Kb
20:10:07.575 INFO [net]  UDP: Speed in/out 4Kb / 2Kb(sec), packets in/out 178/192, total in/out: 123Kb / 89Kb
20:10:48.541 INFO [discover]  Write Node statistics to DB: 829 nodes.
You can also run other example programs, such as these:

./gradlew run -PmainClass=org.ethereum.samples.BasicSample
./gradlew run -PmainClass=org.ethereum.samples.FollowAccount
./gradlew run -PmainClass=org.ethereum.samples.PendingStateSample
./gradlew run -PmainClass=org.ethereum.samples.PriceFeedSample
./gradlew run -PmainClass=org.ethereum.samples.PrivateMinerSample
./gradlew run -PmainClass=org.ethereum.samples.TransactionBomb
For more details about EthereumJ, see this page:

https://github.com/ethereum/ethereumj

10.12 Java Ethereum Smart Contract Example
In the chapter's final Java example, you'll learn how to create an Ethereum Smart Contract. To that, you will need to use Solidity, the designated programming language for Ethereum Smart Contracts, which can be downloaded from the following web site:

https://github.com/ethereum/solidity/releases

(In this book, the Solidity downloaded is version 0.5.7.) For Windows, just look for an archive named solidity-windows.zip. Download it and unzip it to a local folder; in this example, you use E:\solidity-windows\. The solc.exe file in the folder is what you need to compile Solidity programs.

For other operating systems, just follow their instructions accordingly.

You will also need Web3j. Please see section 10.10 for download details. Again, you assume it is downloaded and unzipped into a local folder named E:\web3j-4.0.1\.

Create a folder named E:\contracts\solidity\. Use a text editor to create a file named Greeter.sol in this folder, and save the content in Example 10.4 into the file. This is a simple Smart Contract application, which essentially has just one function, greet(), which just returns the value in a String variable named greeting.

EXAMPLE 10.4 THE SOLIDITY SMART CONTRACT PROGRAM
pragma solidity >0.4.17;
contract mortal {
    address owner;
    function mortal() public { owner = msg.sender; }
    function kill() public { if (msg.sender == owner) selfdestruct(owner); }
}
contract greeter is mortal {
    string greeting;
    // constructor
    function greeter(string _greeting) public {
        greeting = _greeting;
    }
    // getter
    function greet() public constant returns (string memory) {
        return greeting;
    }
}
From a Windows terminal, go to the E:\contracts\solidity\ folder, and type in the following command to compile the Solidity program:

E:\solidity-windows\solc Greeter.sol --bin --abi --optimize -o ../build
This will create the following two .bin files and two .abi files in E:\contracts\build\ folder:

E:\contracts\build\Greeter.bin
E:\contracts\build\Greeter.abi
E:\contracts\build\Mortal.bin
E:\contracts\build\Mortal.abi
For more information about Solidity programming, see the following site:

https://solidity.readthedocs.io/

Then, from the E:\contracts\build\ folder, type in the following command to create a Java project for the Ethereum Smart Contract:

E:\web3j-4.0.1\bin\web3j solidity generate ./greeter.bin ./greeter.abi -o ../../src/main/java 
This will create the Java project in the E:\src\ folder, which has the following structure and contains a Java program called Greeter.java:

E:\ 
   \src
       \main
            \java
                 \Greeter.java
Example 10.5 is the generated Greeter.java program.

EXAMPLE 10.5 THE WEB3J-GENERATED GREETER.JAVA PROGRAM
import java.math.BigInteger;
import java.util.Arrays;
import java.util.Collections;
import org.web3j.abi.FunctionEncoder;
import org.web3j.abi.TypeReference;
import org.web3j.abi.datatypes.Function;
import org.web3j.abi.datatypes.Type;
import org.web3j.abi.datatypes.Utf8String;
import org.web3j.crypto.Credentials;
import org.web3j.protocol.Web3j;
import org.web3j.protocol.core.RemoteCall;
import org.web3j.protocol.core.methods.response.TransactionReceipt;
import org.web3j.tx.Contract;
import org.web3j.tx.TransactionManager;
import org.web3j.tx.gas.ContractGasProvider;
 
/**
 * <p>Auto generated code.
 * <p><strong>Do not modify!</strong>

 * <p>Please use the <a href="<a href="https://docs.web3j.io/command_line.html">https://docs.web3j.io/command_line.html</a>">web3j command line tools</a>,
 * or the org.web3j.codegen.SolidityFunctionWrapperGenerator in the 
 * <a href="<a href="https://github.com/web3j/web3j/tree/master/codegen">https://github.com/web3j/web3j/tree/master/codegen</a>">codegen module</a> to update.
 *
 * <p>Generated with web3j version 4.0.1.
 */
public class Greeter extends Contract {
    private static final String BINARY = "608060405234801561001057600080
fd5b506040516102f03803806102f08339810180604052602081101561003357600080fd
5b81019080805164010000000081111561004b57600080fd5b8201602081018481111561
005e57600080fd5b815164010000000081118282018710171561007857600080fd5b5050
600080546001600160a01b0319163317905580519093506100a492506001915060208401
906100ab565b5050610146565b8280546001816001161561010002031660029004906000
52602060002090601f016020900481019282601f106100ec57805160ff19168380011785
55610119565b82800160010185558215610119579182015b828111156101195782518255
916020019190600101906100fe565b50610125929150610129565b5090565b6101439190
5b80821115610125576000815560010161012f565b90565b61019b806101556000396000
f3fe608060405234801561001057600080fd5b50600436106100365760003560e01c8063
41c0e1b51461003b578063cfae321714610045575b600080fd5b6100436100c2565b005b
61004d6100da565b60408051602080825283518183015283519192839290830191850190
80838360005b8381101561008757818101518382015260200161006f565b505050509050
90810190601f1680156100b45780820380516001836020036101000a0319168152602001
91505b509250505060405180910390f35b6000546001600160a01b03163314156100d857
33ff5b565b60018054604080516020601f60026000196101008789161502019095169490
940493840181900481028201810190925282815260609390929091830182828015610165
5780601f1061013a57610100808354040283529160200191610165565b82019190600052
6020600020905b81548152906001019060200180831161014857829003601f168201915b
505050505090509056fea165627a7a72305820855e7ee1fb28333dc0c79be44c87e61ad0
a03ed6e471852ff59c26020f966aa30029";
 
    public static final String FUNC_KILL = "kill";
 
    public static final String FUNC_GREET = "greet";
 
    @Deprecated
    protected Greeter(String contractAddress, Web3j web3j, Credentials credentials, BigInteger gasPrice, BigInteger gasLimit) {
        super(BINARY, contractAddress, web3j, credentials, gasPrice, gasLimit);
    }
 
    protected Greeter(String contractAddress, Web3j web3j, Credentials credentials, ContractGasProvider contractGasProvider) {
        super(BINARY, contractAddress, web3j, credentials, contractGasProvider);
    }
 
    @Deprecated

    protected Greeter(String contractAddress, Web3j web3j, TransactionManager transactionManager, BigInteger gasPrice, BigInteger gasLimit) {
        super(BINARY, contractAddress, web3j, transactionManager, gasPrice, gasLimit);
    }
 
    protected Greeter(String contractAddress, Web3j web3j, TransactionManager transactionManager, ContractGasProvider contractGasProvider) {
        super(BINARY, contractAddress, web3j, transactionManager, contractGasProvider);
    }
 
    public RemoteCall<TransactionReceipt> kill() {
        final Function function = new Function(
                FUNC_KILL, 
                Arrays.<Type>asList(), 
                Collections.<TypeReference<?>>emptyList());
        return executeRemoteCallTransaction(function);
    }
 
    public RemoteCall<String> greet() {
        final Function function = new Function(FUNC_GREET, 
                Arrays.<Type>asList(), 
                Arrays.<TypeReference<?>>asList(new TypeReference<Utf8String>() {}));
        return executeRemoteCallSingleValueReturn(function, String.class);
    }
 
    @Deprecated
    public static Greeter load(String contractAddress, Web3j web3j, Credentials credentials, BigInteger gasPrice, BigInteger gasLimit) {
        return new Greeter(contractAddress, web3j, credentials, gasPrice, gasLimit);
    }
 
    @Deprecated
    public static Greeter load(String contractAddress, Web3j web3j, TransactionManager transactionManager, BigInteger gasPrice, BigInteger gasLimit) {
        return new Greeter(contractAddress, web3j, transactionManager, gasPrice, gasLimit);
    }
 
    public static Greeter load(String contractAddress, Web3j web3j, Credentials credentials, ContractGasProvider contractGasProvider) {
        return new Greeter(contractAddress, web3j, credentials, contractGasProvider);
    }
 
    public static Greeter load(String contractAddress, Web3j web3j, TransactionManager transactionManager, ContractGasProvider contractGasProvider) {
        return new Greeter(contractAddress, web3j, transactionManager, contractGasProvider);
    }
 
    public static RemoteCall<Greeter> deploy(Web3j web3j, Credentials credentials, ContractGasProvider contractGasProvider, String _greeting) {
        String encodedConstructor = FunctionEncoder.encodeConstructor(Arrays.<Type>asList(new org.web3j.abi.datatypes.Utf8String(_greeting)));
        return deployRemoteCall(Greeter.class, web3j, credentials, contractGasProvider, BINARY, encodedConstructor);
    }
 
    public static RemoteCall<Greeter> deploy(Web3j web3j, TransactionManager transactionManager, ContractGasProvider contractGasProvider, String _greeting) {
        String encodedConstructor = FunctionEncoder.encodeConstructor(Arrays.<Type>asList(new org.web3j.abi.datatypes.Utf8String(_greeting)));
        return deployRemoteCall(Greeter.class, web3j, transactionManager, contractGasProvider, BINARY, encodedConstructor);
    }
 
    @Deprecated
    public static RemoteCall<Greeter> deploy(Web3j web3j, Credentials credentials, BigInteger gasPrice, BigInteger gasLimit, String _greeting) {
        String encodedConstructor = FunctionEncoder.encodeConstructor(Arrays.<Type>asList(new org.web3j.abi.datatypes.Utf8String(_greeting)));
        return deployRemoteCall(Greeter.class, web3j, credentials, gasPrice, gasLimit, BINARY, encodedConstructor);
    }
 
    @Deprecated
    public static RemoteCall<Greeter> deploy(Web3j web3j, TransactionManager transactionManager, BigInteger gasPrice, BigInteger gasLimit, String _greeting) {
        String encodedConstructor = FunctionEncoder.encodeConstructor(Arrays.<Type>asList(new org.web3j.abi.datatypes.Utf8String(_greeting)));
        return deployRemoteCall(Greeter.class, web3j, transactionManager, gasPrice, gasLimit, BINARY, encodedConstructor);
    }
}
Next, use Web3j to create a digital wallet. The procedure is the same as illustrated in section 10.10. Enter the password when prompted, and save the wallet in a folder on your E:\ drive.

E:\web3j-4.0.1\bin\web3j wallet create
The digital wallet will be something like the following, where d517e874a888b58d02dad75c26f2a7ddec14f07b is the wallet ID.

E:\UTC--2019-04-12T03-47-43.931058900Z--d517e874a888b58d02dad75c26f2a7ddec14f07b.json
You will need this wallet password and ID later. Next go to the Infura (https://infura.io/) web site to register an account, create a new project, and copy out the secret key. Infura is an online platform that provides a wide variety of tools to connect your applications to Ethereum. For more information about using Infura with Web3j, see the following site:

https://docs.web3j.io/infura.html

Example 10.6 shows a Java program that can use the Greeter.java program to deploy the Smart Contract and execute the Smart Contract. In this program, the rinkebyKey is the project ID or token ID you got from Infura, and the walletFilePassword and walletId are what you have created using Web3j. Please update them with your own values.

EXAMPLE 10.6 THE GREETING.JAVA SMART CONTRACT PROGRAM
import java.io.IOException;
import java.util.concurrent.ExecutionException;
import org.web3j.crypto.CipherException;
import org.web3j.crypto.Credentials;
import org.web3j.crypto.WalletUtils;
import org.web3j.protocol.Web3j;
import org.web3j.protocol.http.HttpService;
import org.web3j.tx.Contract;
 
public class Greeting {
  public static void main(String[] args) throws IOException, CipherException, ExecutionException, InterruptedException {
 
      String rinkebyKey = "498d65b077ea40ae9aeb2bbb014947cc";
      String rinkebyUrl = "https://rinkeby.infura.io/" + rinkebyKey;
      Web3j web3j = Web3j.build(new HttpService(rinkebyUrl));
 
      String walletFilePassword = "0000000000";
      String walletId = "d517e874a888b58d02dad75c26f2a7ddec14f07b";
      String walletSource = "E:\\UTC--2019-04-12T03-47-43.931058900Z--" + walletId + ".json";
      Credentials credentials = WalletUtils.loadCredentials(walletFilePassword, walletSource);
 
      try {
          //Deploy Smart Contract with a Hello Smart Contract message
          Greeter greeter = Greeter.deploy(web3j, credentials, Contract.GAS_PRICE, Contract.GAS_LIMIT, "Hello Smart Contract!!!").send();

          //Display Smart Contract address
          System.out.println(greeter.getContractAddress());
          //Execute Smart Contract's greet() function
          System.out.println(greeter.greet().send());
      }catch(Exception e){
          System.out.println(e.toString());
      }
  }
}
You can compile and run the Greeting.java and Greeter.java programs by typing the following commands:

javac -classpath ".;E:\\web3j-4.0.1\\lib\\*" Greeter.java Greeting.java
java -classpath ".;E:\\web3j-4.0.1\\lib\\*" Greeting
If everything is correct, you should see a “Hello Smart Contract” message on the screen; otherwise, it will display an error message to explain what is going wrong. A common error is “insufficient funds for gas * price + value.” This is simply because you have not got enough Ether coin in your account. You can either get real Ether:

https://github.com/ethereum/wiki/wiki/Getting-Ether

https://www.ethereum.org/ether

or get Ether for testing:

https://faucet.rinkeby.io/

10.13 Go Further: Choosing a Blockchain Platform
If you want to go further with blockchain, whatever your applications might be—cryptocurrency, healthcare, manufacturing, supply chains, or the IoT—the next and most important step is to choose a suitable blockchain platform and then develop your own decentralized applications, known as DApps. Decentralized applications are different from traditional centralized applications, such as Google, Facebook, or Amazon, where the contents are owned by a central entity. For decentralized applications, the contents are owned by the users. The following is a list of popular blockchain platforms:

Bitcoin (https://bitcoin.org/en/) This is the first blockchain platform, where it all started. This platform is solely designed for one purpose only, which is for the crypocurrency Bitcoin (BTC). The software implementation of Bitcoin is called Bitcoin Core (https://bitcoincore.org/), also called Bitcoin client. Bitcoin Core is written in C++. Today, Bitcoin is the most successful digital currency, with billions of dollars invested in it around the world.
Ethereum (https://www.ethereum.org/) Ethereum was founded in November 2013, by Vitalik Buterin, a Russian-Canadian programmer, when he was only 19 years old! Unlike the Bitcoin platform, the Ethereum platform can do more than just cryptocurrency. Ethereum is written in a Turing-complete language, which includes seven different programming languages. Featuring smart contract functionality, Ethereum is an open source software platform that enables developers to build and deploy decentralized applications based on blockchain technology. Ethereum has its own cryptocurrency, Ether (ETH), and its own programming language, Solidity, a contract-oriented programming language for writing smart contracts.
Eris (https://monax.io/platform/) Built on the Ethereum blockchain, Eris is another free, open platform for building, testing, maintaining, and operating decentralized applications. Eris makes it easy and simple to implement smart contracts.
IBM Blockchain (https://www.ibm.com/blockchain) IBM Blockchain, which is based on the open source Hyperledger Fabric, is a public cloud service that aims for business customers to build secure blockchain networks. Hyperledger Fabric is different from traditional blockchain networks, which can't support private transactions or confidential contracts, which are key for businesses. Hyperledger Fabric addresses this issue by keeping private transactions private and keeping the specific data to be accessible only to who needs to know.
NEO (https://neo.org/) NEO is a nonprofit community-driven blockchain platform. It aims to create a “smart economy,” by utilizing digitize assets, digital identity, and smart contracts. Using a distributed network, Neo uses an interesting consensus mechanism that can improve its scalability.



