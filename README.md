# Payment Splitter

This is how to set up and use [OpenZeppelin's Payment Splitter](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.9.6/contracts/finance/PaymentSplitter.sol).

## Deployment

Obtain the payee addresses and decide on the ratio for the payment splitter.
The ratio must be in whole numbers. For example, if you want to split payments at 1.5% and 3.5%, you can use the ratio 3 : 7.

Deploy the contract.

```sh
forge create -i --rpc-url https://nodes.sequence.app/sepolia lib/openzeppelin-contracts/contracts/finance/PaymentSplitter.sol:PaymentSplitter \
--constructor-args "[0xe8db071f698aBA1d60babaE8e08F5cBc28782108,0xE450ae2D6E1E6c09a0c2e355554Df9EE904B90eA]" "[3, 7]"
```

## Usage

When you need payments split, use the deployed contract address as the payment/fee receiver.

For royalties collected by [Sequence's ERC-721/1155](https://github.com/0xsequence/contracts-library/blob/master/src/tokens/common/ERC2981Controlled.sol), this is done using the `setDefaultRoyalty(address receiver, uint96 feeNumerator)` function.

```sh
cast send -i <token_addr> "setDefaultRoyalty(address,uint96)" <splitter_addr> <fee_numerator>
```

For other integrations, like OpenSea, you may have to use the provided interface.

The payment splitter works on a "pull" mechanism. Payments made to the splitter are held by the splitter contract until the receiver is ready to pull the payment.

The receiver can pull the payment using the `release` function. Anyone can call this function to pull payments for the receiver.

```sh
cast send -i <splitter_addr> "release(address)" <receiver_addr>
```
Payments made in ERC-20 tokens use a similar function.

```sh
cast send -i <splitter_addr> "release(address,address)" <token_addr> <receiver_addr>
```
