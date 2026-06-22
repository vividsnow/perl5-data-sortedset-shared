# Data::SortedSet::Shared

A shared-memory **sorted set** (ZSET) for Linux: int64 members each carrying a
double score, kept in score order on an order-statistics **B+tree** with a
member→score hash index. `rank` / range / `pop` are O(log n) (ranges scan
sequentially through linked leaves); `score` / `exists` are O(1). Usable
concurrently across processes via a write-preferring futex rwlock that recovers
if a holder dies.

```perl
use Data::SortedSet::Shared;
my $z = Data::SortedSet::Shared->new(undef, 1_000_000);
$z->add(42, 1500);
$z->incr(42, 50);
my @top  = $z->rev_range_by_rank(0, 9);          # top 10 by score
my $rank = $z->rank(42);
my @band = $z->range_by_score(1400, 1600);
my ($m, $s) = $z->pop_min;
```

The total order is `(score, member)` — equal scores break by member id, so rank
is well-defined and `pop_min`/`pop_max` are deterministic. Members are integers;
map string keys to ids yourself. Scores must not be NaN. **Linux-only**, 64-bit
Perl.

See `perldoc Data::SortedSet::Shared` for the full API and `eg/leaderboard.pl`
for a worked example.

## Install

    perl Makefile.PL && make && make test && make install

## License

Same terms as Perl itself.
