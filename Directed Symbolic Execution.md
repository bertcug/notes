# <div align="center">Directed Symboilc Execution</div>
##KLEE Test
**KLEE不是Directed Symbolic Execution**
- 待测试代码
```C
int get_sign(int x) {
  if (x == 0)
    return 0;

  if (x < 0)
    return -1;
  else 
    return 1;
}
```
- 测试代码
```C
int main() {
  int a;
  klee_make_symbolic(&a, sizeof(a), "a");
  return get_sign(a);
}
```
- 输出
```
$ ktest-tool --write-ints klee-last/test000001.ktest 
ktest file : 'klee-last/test000001.ktest'
args       : ['get_sign.bc']
num objects: 1
object    0: name: 'a'
object    0: size: 4
object    0: data: 1
$ ktest-tool --write-ints klee-last/test000002.ktest  
...
object    0: data: -2147483648
$ ktest-tool --write-ints klee-last/test000003.ktest 
...
object    0: data: 0
```

#Directed Symboilc Execution
> **automatically finding program executions that reach a particular target line**
> 应用场景：**a developer may want to confirm that a bug reported by a static analysis tool on a particular line is a true positive**

文章提出了两种新方案，一种改进方案：
- **SDSE** （shortest-distance symbolic execution）
  uses a distance metric in an interprocedural control flow graph to guide symbolic execution toward a particular target
- **CCBSE** (call-chain-backward symbolic execution)
  iteratively runs forward symbolic execution, starting in the function containing the target line, and then jumping backward up the call chain until it finds a feasible path from the start of the program.
- **Mix-CCBSE**
  which alternates CCBSE with another (forward) search strategy.

We compare these three with several existing strategies from the literature on a suite of **six GNU Coreutils programs**. We find that **SDSE** performs extremely well in many cases but may fail badly.**CCBSE** also performs quite well, but imposes additional overhead that sometimes makes it slower than SDSE. Considering all our benchmarks together, **Mix-CCBSE** performed best on average, combining to good effect the features of its constituent components.

## SDSE
The basic idea of SDSE is to prioritize program branches that correspond to the shortest path-to-target in the ICFG
- which prioritizes the path with the shortest distance to the target line as computed over an interprocedural control-flow graph (ICFG)
- inspired by the heuristic used in the coverage-based search strategy from KLEE
- the strategy we present has not been specifically described nor has it been applied to directed symbolic execution

##CCBSE
> which starts at the target line and works backward until it finds a realizable path from the start of the program, using standard forward (interprocedural) symbolic execution as a subroutine.

suppose the target line `l` is inside function `f`. CCBSE begins forward symbolic execution from the start of `f`, yielding a set of partial interprocedural paths `pf` that start at `f`, possibly call other functions, and lead to `l`; in a sense, these partial paths summarize selected behavior of `f`. Next, CCBSE runs forward symbolic execution from the start of each function `g` that calls `f`, searching for paths that end at calls to `f`. For each such path `p`, it attempts to continue down paths `p'` in `pf` until reaching `l`,adding all feasible extended paths `p` + `p'`to `pg`. The process continues backward up the call chain until CCBSE finds a path from the start of the program to `l`.

**Notice that by using partial paths to summarize function behavior, CCBSE can reuse the machinery of symbolic execution to concatenate paths together**

##mixed-strategy CCBSE
which combines CCBSE with another forward search. In Mix-CCBSE, we alternate CCBSE with some forward search strategy S. If S encounters a path p that was constructed in CCBSE, we try to follow p to see if we can reach the target line, in addition to continuing S normally. In this way,Mix-CCBSE can perform better than CCBSE and S run separately—compared to CCBSE, it can jump over many function calls from the program start to reach the paths being constructed; and compared to S, it can short-circuit the search once it encounters a path built up by CCBSE.

*We implemented SDSE, CCBSE, and Mix-CCBSE in Otter, a C source code symbolic executor we previously developed* **Otter针对C代码**
[Reisner E, Song C, Ma K K, et al. Using Symbolic Evaluation to Understand Behavior in Configurable Software Systems`[C]`// ACM/IEEE International Conference on Software Engineering. 2010:445-454.](http://drum.lib.umd.edu/bitstream/handle/1903/9779/CS-TR-4946.pdf;jsessionid=57ABB4159841CCB8AEF1CBE9437AE860?sequence=1)
