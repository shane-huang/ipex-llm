# Coding Copilot with Local LLMs: Experience and Tips 


LLM-based coding copilots have become indispensable tools for enhancing productivity and code quality. `Continue` stands out as a popular open-source choice among these tools - it works seamlessly with fully local LLM setup and is available as an extension in Visual Studio Code. 
>For instructions on how to use `Continue` with local LLMs running on Intel GPU platforms, please refer to the [Quickstart Guide](https://ipex-llm.readthedocs.io/en/latest/doc/LLM/Quickstart/continue_quickstart.html).

This blog aims to share my experiences of running `Continue` with several local coding models around size 7B. The models are compared across a variety of coding tasks — from generating new code snippets to explaining algorithms. Hopefully this can help fellow developers choose the right model for their specific needs. 


## Model Choices

When choosing candidate models, two leaderboards are used as major references, i.e. [EvalPlus Leaderboard](https://evalplus.github.io/leaderboard.html), and [BigCode Leaderboard](https://huggingface.co/spaces/bigcode/bigcode-models-leaderboard).

Finally I selected the following models for evaluation:

- [Magicoder-S-DS-6.7B](https://huggingface.co/ise-uiuc/Magicoder-S-DS-6.7B) (transformers == 4.37.2)
- [OpenCodeInterpreter-DS-6.7B](https://huggingface.co/m-a-p/OpenCodeInterpreter-DS-6.7B) (transformers == 4.37.2)
- [starcoder2-7b](https://huggingface.co/bigcode/starcoder2-7b) (transformers == 4.39.3)
- [codegemma-7b](https://huggingface.co/google/codegemma-7b) (transformers == 4.38.1)
- [CodeLlama-7b](https://huggingface.co/meta-llama/CodeLlama-7b-hf) (transformers==4.37.2)
- [CodeQwen1.5-7B-Chat](https://huggingface.co/Qwen/CodeQwen1.5-7B-Chat) (transformers==4.37.2)


## Code Generation Task: Pandas analysis

In this task, I asked the model to generate a Python function that takes a CSV file as input and perform some filtering and aggregation operations using the `pandas` library. 

> For code generation, I used `^I` in a code file and input the full prompt in the popped-up textbox. 

**The full prompt is as follows**:

```text
Write a Python function that takes a CSV file as input and performs the following operations using the Pandas library:
- Loads the CSV into a DataFrame.
- Filters the DataFrame to include only rows where a specified column (e.g., 'Age') is greater than a certain value (e.g., 30).
- Computes the mean of another column (e.g., 'Salary') for the filtered data.
- Returns the mean value.
```

I asked each model to generate the code several times. Most of the time, `Magicoder`, `OpenCodeInterpreter` and `CodeQWen` can generate correct code with meaningful comments. `StarCoder2` can also generate correct code but with less comments. `Codegemma` generated random meaningless characters, maybe due to improper template. `codellama` does not work properly either.  


<details> <summary> <code>Magicoder</code> Detailed Results </summary>

```python
import pandas as pd

def process_csv_file(filepath, column_to_filter, filter_value, column_to_compute_mean):
    # Load CSV from filepath into DataFrame
    df = pd.read_csv(filepath)

    # Filter DataFrame, keeping only rows where column_to_filter > filter_value
    filtered_df = df[df[column_to_filter] > filter_value]

    # Compute mean of column_to_compute_mean
    mean_value = filtered_df[column_to_compute_mean].mean()

    return mean_value

# Example usage:
meanSalary = process_csv_file('test.csv', 'Age', 30, 'Salary')
print('Mean Salary of people above age 30:', meanSalary)
```
</details>

<details> <summary> <code>OpenCodeInterpreter</code> Detailed Results </summary>

```python
import pandas as pd

def compute_mean_salary(filename, column_name, value):
    # Load the CSV file into a DataFrame
    df = pd.read_csv(filename)

    # Filter the DataFrame based on the specified column and value
    filtered_data = df[df[column_name] > value]

    # Compute the mean of the 'Salary' column for the filtered data
    mean_salary = filtered_data['Salary'].mean()

    # Return the mean salary
    return mean_salary
```
</details>

<details> <summary> <code>StarCoder2</code>  Detailed Results </summary>

```python
import pandas as pd
  
def process_data(input_file, column_name, age, column_value):
	df = pd.read_csv(input_file)
	filtered_data = df[df[column_name] >= age]
	mean_value = filtered_data[column_value].mean()
	return mean_value
  
mean_value = process_data(
    'test.csv',
    'Age',
    30,
    'Salary'
)
print(mean_value)
```

</details>

<details> <summary> <code>CodeGemma</code>  Detailed Results </summary>

The output is mostly random characters. May need further tuning on templates.
```python
 defensi casio kask pank kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask kask ...
```
</details>

<details> <summary> <code>CodeLlama</code>  Detailed Results </summary>

```
import pandas Series using the row containing rows where a containing the rows that have values greater than the value1 for items where values in the 'Age values in the monthlyearnerelyearcondition2Continent unit column1 is greater than a specific value is greater thnan the value.
column. Returns a minimum  value.
a specified threshold value
than. <|im_threshold value ('Age' Then outputs the necessary value. Finally, performs the mean of aparameter specified value age column and displays its returnes the mean of the ages column in the filtered ages metathe column of interest in the REST End heavy demand containing values and returns a same means of a specified age column and returns different value and population column of a result that greata mean salary values in the DataFrame in the filtered data set contents determine requested agein interest values.
<|output|matches2 returns a pandas DataFrame the 
```python






<|
```
</details>


<details> <summary> <code>CodeQwen</code>  Detailed Results </summary>

```python
import pandas as pd
def compute_mean_salary(csv_file, target_column, threshold, filter_column):
    # Load the CSV into a DataFrame
    df = pd.read_csv(csv_file)
    
    # Filter the DataFrame
    filtered_df = df[df[filter_column] > threshold]
    
    # Compute the mean of the specified column
    mean_salary = filtered_df[target_column].mean()
    
    return mean_salary
```
</details>

### Test Code Generation Task

In this task, I asked the model to generate unit test for an algorithm which finds the prime factors of an integer. 

>For unit test generation, I used `^L` to add selected code as context and use `/test` built-in instruction.

The original code is as follows:

```python
def find_prime_factors(n):
    i = 2
    factors = []
    while i * i <= n:
        if n % i:
            i += 1
        else:
            n //= i
            factors.append(i)
    if n > 1:
        factors.append(n)
    return factors
```

The results show that, `Magicoder`, `OpenCodeInterpreter` and `CodeQwen` generally produce correct test code that covers various scenarios. However, there're occasional formatting or number errors that can lead to runtime failure. `CodeGemma`still generates random characters, likely due to an improper template. `CodeLlama` just produces irrelevant content. 

<details> <summary> <code>Magicoder</code>  Detailed Results </summary>

```python
import unittest
from task1 import find_prime_factors

class TestPrimeFactors(unittest.TestCase):

    def test_find_prime_factors(self):
        self.assertEqual(find_prime_factors(1), [])
        self.assertEqual(find_prime_factors(2), [2])
        self.assertEqual(find_prime_factors(3), [3])
        self.assertEqual(find_prime_factors(4), [2, 2])
        self.assertEqual(find_prime_factors(5), [5])
        self.assertEqual(find_prime_factors(10), [2, 5])
        self.assertEqual(find_prime_factors(17), [17])
        self.assertEqual(find_prime_factors(20), [2, 2, 5])
        self.assertEqual(find_prime_factors(33), [3, 11])
        self.assertEqual(find_prime_factors(131), [131])

if __name__ == '__main__':
    unittest.main()
```
</details> 

<details> <summary> <code>OpenCodeInterpreter</code>  Detailed Results </summary>

```python
import unittest

def find_prime_factors(n):
    i = 2
    factors = []
    while i * i <= n:
        if n % i:
            i += 1
        else:
            n //= i
            factors.append(i)
    if n > 1:
        factors.append(n)
    return factors
    
class TestTask1(unittest.TestCase):

    def setUp(self):
        # Initilization for all test cases to be conducted
        pass

    def tearDown(self):
        # Cleaning up after each test_case
        pass
    
    def test_Positive(self):
        # Test with positive integer
        self.assertEqual(find_prime_factors(1), [])
        self.assertEqual(find_prime_factors(2), [2])
        self.assertEqual(find, [3,3]
        self.assertEqual(find_prime_factors(7), [7])
        self.assertEqual(find_prime_factors(8), [2, 2, 2])
        self.assertEqual(find_prime_factors(9), [3, 3])
        self.assertEqual(find_prime_factors(15), [3, 5])
        self.assertEqual(find_prime_factors(24), [2, 2, 2, 3])
        self.assertEqual(find_prime_factors(84), [2, 17])
        self.assertEqual(find_prime_factors(100), [2, 2, 5, 5])

    def test_ZeroAndNegative(self):
        # Test with 0 and negative numbers
        self.assertEqual(find_prime_factors(0), [])
        self.assertEqual(find_prime_factors(-1), [])
        self.assertEqual(find_prime_factors(-24), [2, 2, 2, 3])
        self.assertEqual(find_prime_factors(-9), [3, 3])
        self.assertEqual(find_prime_factors(-15), [3, 5])
        self.assertEqual(find_prime_factors(-84), [2, 17])

    def test_BigNumber(self):
        # Test with big numbers
        import math
        big_number = math.pow(10, 8)
        self.assertEqual(find_prime_factors(int(big_number)), [2]*int(big_number))

if __name__ == "__main__":
    unittest.main()
```

</details> 

<details> <summary> <code>CodeGemma</code>  Detailed Results </summary>
Meaningless random characters. Will not show here. 
</details>

<details> <summary> <code>CodeLlama</code>  Detailed Results </summary>

```python
Remember every line numbers and comments
Remember and your code output.py
if you are given 
For specific details responses
*
standard out – You receive sample test(17 
overview_collegit's a.py
You might help_p
def test
@suretisfin1, logic
def test(n_hash
import module: 'n code

1, test help, test for find_prime(n, test.the prime(n):
(num):

   prime(n):
   import random_factors(n):
   # and test, i):
   if n if n, i = tuple(22, prime(2
def is_elig(25, help, test, test
           for argument(n):
11, test(n):
       factors(5, test:
p, comprehension: i from 25, 3
           .            3, test:
     7


   we have(5, and the gcd(21:
   if True:
       prime(f
  5

       i2
   factors = []:
   elif n //d == []:
  1
       while n <= 1
while i:
            5:
   import:
  print(n:
   #print(x <= 3:print(3:
       if n = i:

           i:
       if 5 // one:
   print(i:
   while n:
while i:
       i:
   if...
   u = 7, we:
       print(3:
       while i: 
       i
       while 5
           i
           j
   n +=
           i eliminators = 2n
            else(i

       n:
   pe
  3
       d
print(e:
import(7:
n
  2 for prime(5
   if 2
num(7, 2 or is_text(7
   if, help(7
el:
1
7
9
1
To_factors:3
def 9
prime(5,2,`For demonstr: always remove
8, return_
:
accept
1
fact 7:
4(

Code
2+
if3,help:
1

```

</details> 


<details> <summary> <code>CodeQwen</code>  Detailed Results </summary>

```python
import unittest

class TestFindPrimeFactors(unittest.TestCase):

    def setUp(self):
        self.input_output_test_data = {
            1: [],
            2: [2],
            3: [3],
            4: [2, 2],
            6: [2, 3],
            8: [2, 2, 2],
            9: [3, 3],
            12: [2, 2, 3],
            28: [2, 2, 7],
            49: [7, 7]
        }

    def test_input_output(self):
        for input_val, expected_output in self.input_output_test_data.items():
            self.assertEqual(find_prime_factors(input_val), expected_output)

    def test_edge_cases(self):
        self.assertEqual(find_prime_factors(0), [])
        self.assertEqual(find_prime_factors(-1), [])
        self.assertEqual(find_prime_factors(1), [])
        self.assertEqual(find_prime_factors(13), [13])

    def tearDown(self):
        pass

if __name__ == '__main__':
    unittest.main()

```
</details>

## Adding Comments

In this task, I asked the model to add comments to a given code snippet. The original code is the same as in the section of [Test Code Generation Task](#test-code-generation-task). 

The results show that, `Magicoder`, `OpenCodeInterpreter` and `CodeQwen` generally works well, while `StarCoder2`, `CodeGemma` and `CodeLlama` does not generate meaningful comments. 

> `/comment` instruction is expected to insert comment into the original code snippet directly but is not always perform insersion properly. In this case, you can use the chatbox to obtain the commented code and copy/paste it back to the original file.

<details> <summary> <code>Magicoder</code>  Detailed Results </summary>

```python
def find_prime_factors(n):
    """This function takes an integer n and returns a list of all its prime factors.
    
    Parameters:
    n (int): An integer to find prime factors of.
    
    Returns:
    list: List of prime factors of the input integer.
    """

    # Initialize i to 2. We will start from 2 because 1 is not a prime number.
    i = 2
    factors = []

    # Continue the loop while i squared is less than or equal to n.
    while i * i <= n:
        # If n is not divisible by i:
        if n % i:
            # Increment i by 1.
            i += 1
        # Else, i is a factor:
        else:
            # Divide n by i.
            n //= i
            # Append i to the factors list.
            factors.append(i)

    # If n is greater than 1, it means that n itself is a prime number and it is a factor.
    if n > 1:
        factors.append(n)

    # Return the list of factors.
    return factors
```
</details>

<details> <summary> <code>OpenCodeInterpreter</code>  Detailed Results </summary>

```python
def find_prime_factors(n):
    # Initializing the variable 'i' to 2 as it is the first prime number
    i = 2
    # List to hold all the prime factors of 'n'
    factors = []
    # Loop until 'i' squared is smaller or equal to 'n'
    while i * i <= n:
        # If 'n' is not divisible by 'i', then increase 'i' by 1
        if n % i:
            i += 1
        else:
            # If 'n' is divisible by 'i', then divide 'n' by 'i'
            # and add 'i' to the list of prime factors
            n //= i
            factors.append(i)
    # If 'n' is still greater than 1, then 'n' is a prime number itself
    # Thus, add 'n' to the list of prime factors
    if n > 1:
        factors.append(n)
    # Return the list of prime factors
    return factors
```
</details>

<details> <summary> <code>StarCoder2</code>  Detailed Results </summary>

Most of the time it does not write meaningful comments. And random irrelavant messages were generated. Will not show output here.
</details>

<details> <summary> <code>CodeGemma</code>  Detailed Results </summary>
Meaningless random characters. Will not show here. 
</details>

<details> <summary> <code>CodeLlama</code>  Detailed Results </summary>

```python
```task1.py * while No more to be.task1.findnext_wj <= taken from the PrimeFactors:
Prime:
           i < len(i:

           print('|                        endif i:
               if n >
           if i:
                 factors.append(i += 1        
                 print(len(i += 1
                if i += 1:
           facotr nemporfind_i
           prime_expr =n
                 returnfactors
consumption;
           return n:
           return 1)
           return n:
               return prime_factors:
               return n:
               return(n:
           return n:
               return n:
               return
```
</details>

<details> <summary> <code>CodeQWen</code>  Detailed Results </summary>

```python
# A function is defined to find prime factors of an input number
def find_prime_factors(n):
    # Initialize an index variable i to 2
    i = 2
    # Create an empty list to hold all the prime factors
    factors = []
    
    # As long as i squared smaller than or equal to n
    while i * i <= n:
        # If n is not divisible by i, increment i by 1
        if n % i:
            i += 1
        else:
            # If n is divisible by i (meaning i is a factor),
            # then divide n by i and append i to the list of factors
            n //= i
            factors.append(i)
    
    # If n is greater than 1 after the above process,
    # append n to the list of factors as it is also a prime factor
    if n > 1:
        factors.append(n)
    
    # Once all the factors have been found and appended, return the list
    return factors

```
</details>

## Code Explanation

In this task, I asked the model to explain a given code snippet, which used less common language features or libraries in Python, e.g. `itertools`. The goal is to check if the explanation is accurate, clear, and detailed enough for someone unfamiliar with the specific Python features or libraries used in the code. 

> For code explanation  I used `^L` to add selected code as context and asked the model to explain the code in chatbox.

The original code is as follows:

```python
import itertools

perms = list(itertools.permutations([1, 2, 3]))
print(perms)
```

The results shows that `MagicCoder`, `OpenCodeInterpreter` and `CodeQwen` produces meaningful explanations with proper formatting most of the time, while `CodeGemma`, `CodeLlama` and `StarCoder2` does not work well. 

<details> <summary> <code>MagicCoder</code>  Detailed Results </summary>
<br/>

This piece of code is intended for permutations of the numbers 1, 2, and 3.

The `permutations` function from the `itertools` library generates all possible (ordered) arrangements of elements from an input iterable. In our case, it generates all possible permutations of the numbers 1, 2, 3.

We specify this iterable as [1, 2, 3], which means the set of elements we are interested in is {1, 2, 3}.

The `list()` function is then applied to the generator object returned by `itertools.permutations([1, 2, 3])`. This converts this generator object into a list that can be printed out.

The `print(perms)` line prints out the list of permutations as a readable output, following the pattern: [(1, 2, 3), (1, 3, 2), (2, 1, 3), (2, 3, 1), (3, 1, 2), (3, 2, 1)].
</details>

<details> <summary> <code>OpenCodeInterpreter</code>  Detailed Results </summary>
<br/>

This code is using the `itertools` module which contains functions for creating iterators for efficient looping. `itertools.permutations` function takes an iterable and produces all possible permutations (rearrangements) of its elements.

Here's a breakdown of the given code:

1. The `itertools.permutations` function generates all permutations of the list `[1, 2, 3]`. Permutations are different orders of elements in the list. For example, `[1, 2, 3]` and `[2, 1, 3]` are different permutations of the same list. The function returns an iterable that can be converted to a list.

2. The resulting `perms` variable is a list of tuples, where each tuple is a permutation of the elements in the original list. For `[1, 2, 3]`, the output would be `[(1, 2, 3), (1, 3, 2), (2, 1, 3), (2, 3, 1), (3, 1, 2), (3, 2, 1)]`.

3. The `print(perms)` statement then prints this list of permutations to the console.

For example, for the list `[1, 2, 3]`, the output would be something like this:

```
[(1, 2, 3), (1, 3, 2), (2, 1, 3), (2, 3, 1), (3, 1, 2), (3, 2, 1)]
```

Each permutation is represented as a tuple.

</details>

<details> <summary> <code>StarCoder2</code>  Detailed Results </summary>
<br/>

```md
run this code, you will get permission [(1, 2, 3), (1, 3, 2), (2, 1, 3), (2, 3, 1), (3, 1, 2), (3, 2, 1) ]

Sometimes generates irrelavant explanations

The import statement imports the permutation function from the itertools package on line 3. The following lines define perms, which is a list, and assigns it to the return value, which in this case, is the permutation function. Python always display a permutations function, which computes all unique permutations of the numbers 1, 2 and 3 that are taken 2 at a time. print(perms) prints this value.

Question 5 Write your own function that substitutes a name and/or an animal into an operation code template(operation_template), or simply uses a redacted(censor) operation code template (operation_template_censored) to create a snippet of text that print statement before or after executing a code block.
```
</details>

<details> <summary> <code>CodeGemma</code>  Detailed Results </summary>

The output is mostly random characters. May need further tuning on templates.
</details>

<details> <summary> <code>CodeLlama</code>  Detailed Results </summary>

```md 
for values_one_permut(permut([))


for_combin)
for)
iom)

Xrange(["0, [
sub)
mange(1, 2, end)
range(perms computing_itertools.count([1,  end)


print()
```

</details>

<details> <summary> <code>CodeQWen</code>  Detailed Results </summary>
<br/>

```md
The given code is a Python script that utilizes the `itertools` module to generate all possible permutations of the list `[1, 2, 3]`. Here's the step-by-step explanation:

1. `itertools` is a Python library that provides functions to work with iterators, which are objects that generate their results one at a time.

2. `itertools.permutations()` function takes an iterable and a length (represented as an integer) as input and returns an iterator that produce all possible permutations of the given iterable of length `n`. In this case, the iterable is the list `[1, 2, 3]` and the length is not specified, so it defaults to all permutations (since no second argument is provided).

3. `itertools.permutations([1, 2, 3])` returns an iterator that produces all permutations of the list `[1, 2, 3]`.

4. To convert the iterator to a list, you can use the `list()` function. `list()` takes an iterator and returns a list containing all the items produced by the iterator.

5. Finally, `print(perms)` displays the list of all permutations.

In summary, this Python script generates all the 6 possible permutations of the list `[1, 2, 3]` and prints them out.
```

</details>
<br/>

In conclusion, using `Magicoder`, `OpenCodeInterpreter` or `CodeQWen` is generally recommended for coding generation, adding comments and code explanation tasks. `StarCoder2` may also be okay for some code generation task but sometimes can produce absolute non-sense. `CodeGemma` and `CodeLlama` may need further prompt tuning for reasonable results.