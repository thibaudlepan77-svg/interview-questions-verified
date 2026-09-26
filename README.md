# Interview questions where every wrong answer is explained

A hundred free practice questions, twenty each for JavaScript, Python, SQL, pandas and NumPy, playable at https://thibaudlepan77-svg.github.io/interview-questions-verified/

Every snippet was run before its answer was written down. JavaScript on Node, Python on CPython in isolated mode, pandas 3.0.5, NumPy 2.4.4. SQL queries had to return exactly the same rows on SQLite and on DuckDB, two engines with no shared code, or the question was thrown out. Each question comes with a short reason for every one of its four options, because the wrong ones are where you learn something.

Fifteen of them are below, three per topic. The rest are on the site, one page per question in [questions.html](https://thibaudlepan77-svg.github.io/interview-questions-verified/questions.html).

## JavaScript

**What does this print?**

```js
console.log(['a', 'b', 'c'].reduceRight((acc, x) => acc + x));
```

- **A.** `abc`
- **B.** `cab`
- **C.** `cba`
- **D.** `undefined`

<details><summary>Answer</summary>

**C**, as Node printed it.

- **A**. Assumes reduceRight processes the array in the same left to right order as reduce. reduceRight always folds the array starting from the last element and moving toward the first.
- **B**. Assumes the last element ends up somewhere in the middle of the result rather than at the very front. reduceRight starts its very first step with the last two elements, so the last element always leads the accumulation.
- **C**. Correct. reduceRight folds the array from right to left, so starting from "c" and appending "b" then "a" produces the string "cba".
- **D**. Assumes reduceRight without an explicit initial value fails the same way reduce does on an empty array. The array here is not empty, so the last element simply becomes the seed and the reduction proceeds normally.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/javascript-02.html)

</details>

**What does this print?**

```js
const response = { data: { user: { id: 7 } } };
const { data: { user: { id } } } = response;
console.log(id);
```

- **A.** `ReferenceError`
- **B.** `{"id":7}`
- **C.** `7`
- **D.** `undefined`

<details><summary>Answer</summary>

**C**, as Node printed it.

- **A**. Assumes id is never declared because the pattern only introduces bindings for data and user.
- **B**. Assumes the innermost pattern binds the whole user object rather than just its id property.
- **C**. Correct. Nested destructuring walks down through data and user and binds id to the number stored three levels deep.
- **D**. Assumes destructuring through several nested levels in one pattern is not valid and silently produces undefined.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/javascript-09.html)

</details>

**What is the exact order of the output?**

```js
const trace = [];
trace.push('start');
setTimeout(() => trace.push('timeout'), 0);
Promise.resolve().then(() => trace.push('microtask'));
trace.push('sync-end');
setTimeout(() => console.log(trace.join(' ')), 0);
```

- **A.** `start sync-end timeout microtask`
- **B.** `start timeout microtask sync-end`
- **C.** `start sync-end microtask timeout`
- **D.** `start microtask sync-end timeout`

<details><summary>Answer</summary>

**C**, as Node printed it.

- **A**. Assumes a zero delay timer fires before the microtask queue drains. Every microtask always runs before the next macrotask, no matter how short the delay.
- **B**. Assumes the timer callback preempts the rest of the current script. The synchronous code always finishes before any timer or microtask gets a turn.
- **C**. Correct. The synchronous code runs to completion first, then the whole microtask queue drains, and only then does the first macrotask fire.
- **D**. Assumes an already resolved promise runs its callback immediately in place. A then callback is always deferred to the microtask queue, even on a settled promise.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/javascript-16.html)

</details>

## Python

**What does this print?**

```python
a = [3, 1, 2]
b = a.sort()
print(b)
```

- **A.** `None`
- **B.** `[1, 2, 3]`
- **C.** `[3, 1, 2]`
- **D.** `True`

<details><summary>Answer</summary>

**A**, as CPython printed it.

- **A**. Correct. list.sort() sorts the list in place and returns None, so b ends up holding None regardless of how a itself changed.
- **B**. Assumes sort() returns the sorted list, the way sorted() does, instead of mutating in place and returning nothing.
- **C**. Assumes sort() returns a reference to the list before it was sorted.
- **D**. Assumes an in place mutating method reports success as a boolean rather than returning None.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/python-02.html)

</details>

**What does this print?**

```python
print(any([]))
```

- **A.** `False`
- **B.** `ValueError`
- **C.** `True`
- **D.** `None`

<details><summary>Answer</summary>

**A**, as CPython printed it.

- **A**. Correct. any() reports whether at least one element of the iterable is truthy, and an empty iterable has no elements at all, so the answer is False.
- **B**. Assumes any() requires at least one element to operate on and raises when given an empty iterable.
- **C**. Confuses any() with all(), assuming an empty iterable vacuously satisfies the condition any() is checking for.
- **D**. Assumes an empty iterable produces no boolean result at all instead of a definite False.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/python-09.html)

</details>

**What does this print?**

```python
a = {1, 2, 3}
b = {2, 3, 4}
print(sorted(b - a))
```

- **A.** `[2, 3, 4]`
- **B.** `[4]`
- **C.** `[1, 2, 3]`
- **D.** `[1]`

<details><summary>Answer</summary>

**B**, as CPython printed it.

- **A**. Assumes the minus operator removes nothing here since the sets overlap heavily. b - a still actively removes any element of b that is also present in a, 2 and 3 are shared and get removed, only 4, which is unique to b, remains.
- **B**. Correct. b - a keeps only the elements of b that are not present in a, 2 and 3 are shared with a and get removed, leaving 4 as the only element unique to b.
- **C**. Assumes the difference of two sets keeps everything except the truly identical set, essentially returning b unchanged. Set difference removes exactly the elements common to both sets, since a and b share 2 and 3, those two elements are dropped from the result.
- **D**. Confuses this difference with a - b computed on the same two sets, which would remove different elements. b - a keeps elements of b missing from a, that is 4 alone, computing it the other way around is what would give 1 as the only leftover element.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/python-16.html)

</details>

## SQL

**What does this query return?**

```sql
CREATE TABLE waitlist (id INTEGER, applicant TEXT, priority INTEGER);
INSERT INTO waitlist VALUES
  (1, 'App1', 1),
  (2, 'App2', 3),
  (3, 'App3', NULL),
  (4, 'App4', 5);

SELECT applicant FROM waitlist WHERE priority NOT IN (1, 2) AND priority IS NOT NULL
```

- **A.**

   ```
   App2
   App3
   App4
   ```
- **B.**

   ```
   App1
   App2
   App4
   ```
- **C.** `(no rows)`
- **D.**

   ```
   App2
   App4
   ```

<details><summary>Answer</summary>

**D**, as SQLite and DuckDB printed it.

- **A**. This keeps App3 despite the explicit IS NOT NULL guard, forgetting that a missing priority fails that check regardless of what NOT IN alone would decide.
- **B**. This keeps App1 as if priority 1 slipped past NOT IN (1, 2), but 1 is literally one of the excluded values and fails that test directly.
- **C**. This assumes the NULL priority anywhere in the table defeats the AND for every row, but the IS NOT NULL guard exists precisely to handle that row without affecting the others.
- **D**. Correct. App2 and App4 have a priority outside the excluded list, App1 matches the excluded value 1, and App3 fails the explicit IS NOT NULL guard.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/sql-02.html)

</details>

**What does this query return?**

```sql
CREATE TABLE contest2 (id INTEGER, name TEXT, points INTEGER);
INSERT INTO contest2 VALUES
  (1, 'Fay', 70),
  (2, 'Gus', 70),
  (3, 'Hana', 65),
  (4, 'Ivo', 60);


SELECT name, points,
       RANK() OVER (ORDER BY points DESC) AS rnk,
       DENSE_RANK() OVER (ORDER BY points DESC) AS drnk
FROM contest2
ORDER BY points DESC, name
```

- **A.**

   ```
   Fay | 70 | 1 | 1
   Gus | 70 | 1 | 1
   Hana | 65 | 3 | 2
   Ivo | 60 | 4 | 3
   ```
- **B.**

   ```
   Fay | 70 | 1 | 1
   Gus | 70 | 1 | 1
   Hana | 65 | 2 | 3
   Ivo | 60 | 3 | 4
   ```
- **C.**

   ```
   Fay | 70 | 1 | 1
   Gus | 70 | 2 | 2
   Hana | 65 | 3 | 3
   Ivo | 60 | 4 | 4
   ```
- **D.**

   ```
   Fay | 70 | 1 | 1
   Gus | 70 | 1 | 1
   Hana | 65 | 2 | 2
   Ivo | 60 | 3 | 3
   ```

<details><summary>Answer</summary>

**A**, as SQLite and DuckDB printed it.

- **A**. Correct. Both functions tie Fay and Gus at 1, then RANK jumps to 3 for Hana because two rows sit above it, while DENSE_RANK moves to 2 with no gap.
- **B**. This swaps the two columns for the non tied rows, giving RANK the gapless numbers that belong to DENSE_RANK and handing DENSE_RANK the skipped numbers that belong to RANK.
- **C**. This applies ROW_NUMBER logic to both columns, breaking the tie between Fay and Gus instead of letting RANK and DENSE_RANK agree on 1 for both of them.
- **D**. This applies DENSE_RANK logic to both columns, so RANK never skips ahead after the tie even though it counts rows above the current one rather than distinct values.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/sql-09.html)

</details>

**What does this query return?**

```sql
CREATE TABLE track_plays (id INTEGER, artist TEXT, track TEXT, play_count INTEGER);
INSERT INTO track_plays VALUES
  (1, 'nova', 'Echo', 5000),
  (2, 'nova', 'Static', 7200),
  (3, 'nova', 'Drift', 7200),
  (4, 'nova', 'Halo', 4100);


SELECT artist, track, play_count,
       DENSE_RANK() OVER (PARTITION BY artist ORDER BY play_count DESC) AS drnk
FROM track_plays
ORDER BY artist, drnk, track
```

- **A.**

   ```
   nova | Drift | 7200 | 1
   nova | Static | 7200 | 1
   nova | Echo | 5000 | 3
   nova | Halo | 4100 | 4
   ```
- **B.**

   ```
   nova | Drift | 7200 | 1
   nova | Static | 7200 | 1
   nova | Echo | 5000 | 2
   nova | Halo | 4100 | 3
   ```
- **C.**

   ```
   nova | Drift | 7200 | 1
   nova | Static | 7200 | 2
   nova | Echo | 5000 | 3
   nova | Halo | 4100 | 4
   ```
- **D.**

   ```
   nova | Halo | 4100 | 1
   nova | Echo | 5000 | 2
   nova | Drift | 7200 | 3
   nova | Static | 7200 | 3
   ```

<details><summary>Answer</summary>

**B**, as SQLite and DuckDB printed it.

- **A**. This is what RANK would produce, also tying the top two tracks at 1 but then skipping ahead to 3 for the next track instead of the gapless 2 DENSE_RANK gives.
- **B**. Correct. Drift and Static tie for the most plays and both get 1, then DENSE_RANK moves straight to 2 for Echo with no gap, and 3 for Halo.
- **C**. This is what ROW_NUMBER would produce, splitting the tied top tracks into two different numbers instead of letting them share the same rank.
- **D**. This orders play_count ascending instead of descending, so the least played track ranks first, the opposite of what DESC in the window ORDER BY specifies.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/sql-16.html)

</details>

## pandas

**What does this print?**

```python
import pandas as pd
s = pd.Series(['1', '2', 'x'])
print(s.astype(int))
```

- **A.** `ValueError`
- **B.** `nan`
- **C.** `0    1\n1    2\n2    NaN\ndtype: float64`
- **D.** `0    1\n1    2\n2    0\ndtype: int64`

<details><summary>Answer</summary>

**A**, as pandas 3.0.5 printed it.

- **A**. Correct. astype(int) tries to convert every entry, and since 'x' cannot be parsed as an integer, the conversion fails for the whole Series and raises a ValueError rather than silently skipping or defaulting that one entry.
- **B**. Assumes the whole operation quietly produces a single missing value instead of raising an error.
- **C**. Assumes an unparseable entry becomes a missing float value while the dtype falls back to float64, rather than the conversion raising an error.
- **D**. Assumes an unparseable entry gets silently replaced by 0 while the rest convert normally.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/pandas-02.html)

</details>

**What does this print?**

```python
import pandas as pd
df = pd.DataFrame({'k': ['b', 'a', 'b', 'a'], 'v': [1, 2, 3, 4]})
print(df.groupby('k')['v'].sum().index.tolist())
```

- **A.** `['b', 'a']`
- **B.** `[0, 1]`
- **C.** `['a', 'b']`
- **D.** `['a', 'a', 'b', 'b']`

<details><summary>Answer</summary>

**C**, as pandas 3.0.5 printed it.

- **A**. Assumes the groups keep the order in which their keys first appeared in the original data.
- **B**. Assumes the index of the aggregated result is a plain positional range instead of the sorted group keys.
- **C**. Correct. groupby sorts its group keys in ascending order by default, so even though b appears first in the data, a comes first in the result.
- **D**. Assumes the index repeats each key once per original row instead of collapsing to one entry per group.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/pandas-09.html)

</details>

**What does this print?**

```python
import pandas as pd
df = pd.DataFrame({'a': [1, 2, 3], 'b': [7, 8, 9]})
df.loc[df['a'] > 1, 'b'] = 0
print(df['b'].tolist())
```

- **A.** `ChainedAssignmentError`
- **B.** `[7, 8, 9]`
- **C.** `[7, 0, 0]`
- **D.** `[0, 0, 0]`

<details><summary>Answer</summary>

**C**, as pandas 3.0.5 printed it.

- **A**. Assumes this recommended single step pattern still triggers the warning that chained assignment triggers.
- **B**. Assumes a loc based write is chained the same way df[mask][col] is, and gets silently dropped.
- **C**. Correct. df.loc[mask, 'b'] = 0 addresses both the rows and the column in one call on df itself, so it is a direct write that succeeds, giving [7, 0, 0].
- **D**. Assumes the write reaches every row of the column instead of only the rows selected by the mask.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/pandas-16.html)

</details>

## NumPy

**What does this print?**

```python
import numpy as np
a = [np.int64(1), np.int64(2)]
print(a)
```

- **A.** `[np.int64(1), np.int64(2)]`
- **B.** `['np.int64(1)', 'np.int64(2)']`
- **C.** `[int64(1), int64(2)]`
- **D.** `[1, 2]`

<details><summary>Answer</summary>

**A**, as NumPy 2.4.4 printed it.

- **A**. Correct. Printing a plain Python list calls repr on each element, so the scalars show their NumPy 2 type wrapper even though print itself uses str on the outer list.
- **B**. Assumes the wrapped reprs come out as quoted text rather than as the literal repr NumPy produces.
- **C**. Assumes the wrapper drops the np module prefix inside a list the way it never does for a bare scalar either.
- **D**. Assumes the elements print the same bare way they would if printed on their own with print.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/numpy-02.html)

</details>

**What does this print?**

```python
import numpy as np
a = np.arange(6)
b = a[[1, 2, 3]]
print(b.base is None)
```

- **A.** `False`
- **B.** `True`
- **C.** `AttributeError`
- **D.** `None`

<details><summary>Answer</summary>

**B**, as NumPy 2.4.4 printed it.

- **A**. Assumes any array built from indexing another array must carry a base pointing back to it.
- **B**. Correct. Fancy indexing produces an independent array that owns its own memory, so it has no source array to point back to and its base is None.
- **C**. Assumes an array produced by indexing has no base attribute to check at all.
- **D**. Assumes base reports the missing marker None as a printed value instead of the array being asked whether its base is that value.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/numpy-09.html)

</details>

**What does this print?**

```python
import numpy as np
a = np.array([3, 1, 2])
r = a.sort()
print(r, a.tolist())
```

- **A.** `None [1, 2, 3]`
- **B.** `None [3, 1, 2]`
- **C.** `[1, 2, 3] [1, 2, 3]`
- **D.** `array([1, 2, 3]) [1, 2, 3]`

<details><summary>Answer</summary>

**A**, as NumPy 2.4.4 printed it.

- **A**. Correct. The in place method a.sort() reorders the array itself and returns None, so printing its result shows None while a itself now holds the sorted values.
- **B**. Assumes a.sort() returns None without actually reordering the array in place.
- **C**. Assumes a.sort() returns the sorted array the way np.sort would, instead of returning None.
- **D**. Assumes the in place method hands back a printed array object rather than the plain value None.

[Play it in the browser](https://thibaudlepan77-svg.github.io/interview-questions-verified/q/numpy-16.html)

</details>

## The full banks

The samples come from larger banks sold as PDFs, questions first and the answer key after, with the same four explanations on every question.

| Bank | Questions | PDF |
|---|---|---|
| JavaScript | 600 | [9 euros](https://ko-fi.com/s/e564f7a936) |
| Python | 600 | [9 euros](https://ko-fi.com/s/00207c81fe) |
| SQL | 502 | [9 euros](https://ko-fi.com/s/5128d84869) |
| pandas | 255 | [9 euros](https://ko-fi.com/s/4b32c5be4f) |
| NumPy | 279 | [9 euros](https://ko-fi.com/s/bc22cdcdd3) |
| SQL, Python, pandas and NumPy | 1,636 | [19 euros](https://ko-fi.com/s/8151d45764) |
| JavaScript, Python and SQL | 1,702 | [19 euros](https://ko-fi.com/s/fa83c4ed59) |

Free PDFs of each twenty question sample are on the site. If an answer looks wrong to you, open an issue with the question and what your own run prints.
