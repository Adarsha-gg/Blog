
How are deterministic wallets keys generated?

So let's take a random number of 128 bits, which is 16 bytes, which is 32 hexadecimals. Always generate this from a cryptographically secure random number generator. Then, "hash" it using a SHA256 hash algorithm which just "hashes" it into a 256-bit output.

Hashing just means that when you give an input, you get a seemingly random output. You could learn about how hashing works [here](https://www.codecademy.com/resources/blog/what-is-hashing/).

Once you have the "hash," take the first 4 bits of it and add it to the original. Now split that 132-bit number into 12 sections of 11 bits each. Now let's assign each section to a word. 

![Pasted image 20241130084502](https://github.com/user-attachments/assets/15b38c59-4066-463d-a27a-46d45c4b7345)

There is a predefined set dictionary of 2,048 words, which maps every single value possible from that 11-bit to a word. Since 2^11 is 2,048, you can be sure that for every permutation of 11 bits that you have, there is a word. Example, 00000000000 may map to "hello," 11111111111 maps to something else, and so on. These mapped 12 words are the recovery code for your account. This makes it easier for recovery, but the problem is if someone gets hold of the words, they can steal all of your funds in all of your accounts. So keep it VERY safe.

Now those bits (which were mapped to words) are added with a specific word "mnemonic" and an optional passphrase that you can add if you want to. Then the whole string (132 bits + "mnemonic" + passphrase) goes through another "hashing" function which gives out a 512-bit output. This is done 2,048 times to slow down brute force attacks if any attackers know some of your words but not all of it. So it works like hash(hash(string)) 2,048 times. The 2,048th hash is your seed phrase.

![Pasted image 20241130083311](https://github.com/user-attachments/assets/7ac3ef1a-7ce1-4ebf-8827-3a0851face60)

That seed phrase is used to generate your "master private key" which is the key that you use to "sign" your transactions. Signing your transactions means proving that the transaction indeed came from you and it wasn't someone else. It is just like signing a cheque, which verifies that it was you who issued it.

To generate the master private key, you AGAIN put the seed phrase into the 512-bit hash, and the left 256 bits of it is your private key and the right 256 bits is your master chain code. The private key is used to derive your public key, and the chain code is there to provide entropy (randomness) for your child keys.

![Pasted image 20241130085637](https://github.com/user-attachments/assets/29f9b740-50f0-49e7-9867-1329f3c205f5)


Now you can use the private key + chain code + index number, where the index number is just a 32-bit number to know the index of the child key that we are deriving to derive a child key. The combined string ("private key + chain code + index number") is again put into the 512-bit hashing algorithm to get another hash which is split to private key and chain code like before (256 to the left and 256 to the right). This is the child key's private key and chain code for that index. You can do this to generate 4 billion child keys, and 4 billion grandchildren for each child and so on.

![Pasted image 20241130091236](https://github.com/user-attachments/assets/9ff1d986-37af-46b8-a660-58649944589f)


So, now that you have unlimited generation of private keys, you can create public keys from them which transform into valid addresses. Every single private key is linked to a public key using a formula: Public key = private key x G, where G is always a known number.

Since there are so many keys to keep track of, we use a system called paths to do so. Let's call your master private key as m. So the 0th indexed child of it would be m/0 and the 1st indexed child of the child would be m/0/1 and so on. Now there is a specific way to use the keys which is:
```
m / purpose' / coin_type' / account' / change / address_index
```
where every single level that you derive has a specific purpose and it can be modified to suit your needs. You now have the tools to generate as many keys as you want for any chain that you want. 


