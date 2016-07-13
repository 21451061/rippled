
# Consensus Algorithm

This directory holds the types and classes needed
to connect consensus to rippled.

## Types

All types must be copy constructible and assignable.

* `CxLgrID_t`
  Represents a ledger identifier.
  Typically a 256-bit hash of the ledger header.

* `CxTxID_t`
  Represents a transaction identifier.
  Typically a 256-bit hash of the transaction data.

* `CxTxSetID_t`
  Represents an identifier of a set of transactions.
  Typically a 256-bit hash of the set's root tree node.

* `CxNodeID_t`
  Represents an identifier for a node that can take positions during
  the consenus process.

* `CxTime_t`
  Encodes absolute times. Used for the close times of ledgers and the
  expiration times of positions.

* `CxPos_t`
  Represents a position on a consensus taken by a participant.
  Typically it encodes the previous ledger identifier, the transaction
  set identifier, the participant, and a sequence number. It also includes
  either the time it was signed or the time it was first seen. It may also
  include additional information such as the participant's public key or
  signature

* `CxTx_t`
  Represent a transaction. Has an identifier and also whatever information
  is needed to add it to a set.

* `CxTxSet_t`
  Represents a set of transactions. It has an identifier and can report
  which transactions it has and provide the actual transaction data.
  If non-const, it can be modified.

* `CxTxSetDiff_t`
  Represents the difference between two sets of transactions.
  Traversable, with value type `std::pair<CxTxID_t, bool>`.

## `CxPos_t`

Represents a position taken by a validator during a consensus round.
Must provide:

static std::uint32_t seqInitial;

static std::uint32_t seqLeave;

std::uint32_t getSequence() const;

CxTime_t getCloseTime() const;

CxTime_t getSeenTime() const;

bool isStale (CxTime_t) const;

CxNodeID_t getNodeID() const;

CxTxSetID_t getPosition() const;

CxLgrID_t getPrevLedger() const;

bool isInitial() const;

bool isBowOut() const;

Json::Value getJson() const;

bool changePosition (CxTxSetID_t const& position, CxTime_t closeTime, CxTime_t now);

bool bowOut (CxTime_t now);


### `CxTx_t`

Represents a transaction.
Must provide:

CxTxID_t getID() const;


### CxTxSet_t

Represents a set of transactions.
Must provide:

CxTxSet_t (CxTxSet_t::mutable_t const&);

CxTxSetID_t getID() const;

bool hasEntry (CxTxID_t const&) const;

bool hasEntry (CxTx_t const&) const;

boost::optional<CxTx_t const> const getEntry (CxTxID_t const&) const;

CxTxSetDiff_t getDifferences(CxTxSet_t const&) const;

## CxTxSet_t::mutable_t

Represents a set of transactions that can be modified.
Must provide:

CxTxSet_t::mutable_t (CxTxSet_t const &);

bool addEntry (CxTx_t const&);

bool removeEntry (CxTxID_t const&);
