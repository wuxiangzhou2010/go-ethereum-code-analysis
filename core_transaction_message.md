# Transactions and message

core/types/transaction.go

transaction 是由EOA发出，并在以太坊P2P网络传输. 而Message 是EVM执行的输入。 两者高度相似， 字段几乎一一对应。
请看

## Transaction 数据结构

```go
type Transaction struct {
    data txdata
    // caches
    hash atomic.Value
    size atomic.Value
    from atomic.Value
}

type txdata struct {
    AccountNonce uint64          `json:"nonce"    gencodec:"required"`
    Price        *big.Int        `json:"gasPrice" gencodec:"required"`
    GasLimit     uint64          `json:"gas"      gencodec:"required"`
    Recipient    *common.Address `json:"to"       rlp:"nil"` // nil means contract creation
    Amount       *big.Int        `json:"value"    gencodec:"required"`
    Payload      []byte          `json:"input"    gencodec:"required"`

    // Signature values
    V *big.Int `json:"v" gencodec:"required"`
    R *big.Int `json:"r" gencodec:"required"`
    S *big.Int `json:"s" gencodec:"required"`

    // This is only used when marshaling to JSON.
    Hash *common.Hash `json:"hash" rlp:"-"`
}

```

## Message 数据结构

```go
// Message is a fully derived transaction and implements core.Message
//
// NOTE: In a future PR this will be removed.
type Message struct {
    to         *common.Address
    from       common.Address
    nonce      uint64
    amount     *big.Int
    gasLimit   uint64
    gasPrice   *big.Int
    data       []byte
    checkNonce bool
}
```

tx 和message 之间的转换通过函数

```go
func (tx *Transaction) AsMessage(s Signer, status uint, gasUsed uint64) (Message, error)
```