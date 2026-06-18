1. In response to the escalating concerns about theft and robbery in urban areas, Maya has taken proactive measures. She's decided to rent a bank locker at XYZ Bank to securely safeguard her valuable jewelry and crucial documents. XYZ Bank employs an innovative digital locking system for its lockers.

Each locker is assigned a unique locker number, denoted as N. To open the locker, one must input the passcode P. This passcode has a distinctive property: the product of all its digits must be equal to the locker number, N. Additionally, the passcode should be the smallest possible combination that fulfills this condition and is greater than or equal to N. For added security, the locker system permits only three attempts to enter the correct passcode. If three unsuccessful attempts are made, the locker will be inaccessible for the next 24 hours.

Given her unfamiliarity with this technology, Maya seeks assistance in the program that can ascertain the correct passcode for a given locker

Read the input from STDIN and print the output to STDOUT. Do not write arbitrary strings anywhere in the program, as these contribute to the standard output, and test cases will fail.


Constraints: 

i) N > 0 Input Format: The input contains an integer N, the locker number. 

Output Format: The only line of output should consist of P, the passcode of the locker.

Sample Input 1: 36

Sample Input 3: 
9 

Sample Output 3: 

9


Explanation 3: The given locker number, N, is 9. 

9=9 

1*9=9 

9*1=9 

Since 9 is the minimum number compared to other possibilities. So, 9 is the passcode of the locker, which is printed as an output.


```python
def passCode(N):

    if N <= 9:
        return str(N)

    digits = []

    for i in range(9, 1, -1):
        while N % i == 0:
            digits.append(i)
            N //= i

    digits.sort()

    result = ""

    for d in digits:
        result += str(d)

    return result


N = int(input())
print(passCode(N))

```
