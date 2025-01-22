Section 2.8# 5, 6, 15 and 19
5. Encrypt howareyou using the affine function . What is the decryption function? Check that it works
6. encrypt messages using the affine function mod 26. Decrypt the ciphertext .
15. ![[Pasted image 20250113120758.png]]
For an affine cipher to be decryptable, *a* must be coprime with the modulus (in this case 30) (B is just a shift), so each encrypted letter is unique.  
The numbers coprime to 30 are: 1, 7, 11, 13, 17, 19, 23, 29 (8 nums).
2. 'a' and 'd'
3. The reasoning behind this is if 10(letter1 - letter2) % 30 = 0 they will collide and both equal 0 as they're both divisible by 30 (using difference to demonstrate this relationship)
```bash
 ╱  ~/Desktop/HPU-Files/Classes/Spring-2025/Cryptography/Labs  cat affine_dups.py
 
import sys
dups = []

for i in range(int(sys.argv[1])):
    for j in range(int(sys.argv[1])):
        if not((int(sys.argv[2]) * i ) - (int(sys.argv[2]) * j) % 30) :
            dups.append((i,j))

print(dups)
  ╱  ~/Desktop/HPU-Files/Classes/Spring-2025/Cryptography/Labs  python3 affine_dups.py 30 10
 
[(0, 0), (0, 3), (0, 6), (0, 9), (0, 12), (0, 15), (0, 18), (0, 21), (0, 24), (0, 27), (1, 1), (1, 4), (1, 7), (1, 10), (1, 13), (1, 16), (1, 19), (1, 22), (1, 25), (1, 28), (2, 2), (2, 5), (2, 8), (2, 11), (2, 14), (2, 17), (2, 20), (2, 23), (2, 26), (2, 29)]
```

5. Number 19th must be done by hand and show or explain your work on each problem. ![[Pasted image 20250113120833.png]]

**Finding Key Length**
```python
'''
Find Key Length
'''
cipher = "bababaaaba"

def find_key_length(longest_key):
    count = 0
    space = " "
    current_max = (0, 0)

    for shift in range(1, longest_key+1):
        count = 0
        ct2 = shift*space+cipher
        for i in range(0, len(cipher)):
            if cipher[i] == ct2[i]:
                count += 1
        print("Number of matches with a shift of "+str(shift)+"= "+str(count))
        current_max = (shift, count) if count > current_max[1] else current_max
    print(f"Max matches with key_length of {current_max[0]}")


find_key_length(3)
```

**Finding Key Length**
```bash
$ python -u "/Users/wyld7k/Desktop/HPU-Files/Classes/Spring-2025/Cryptography/Labs/question-19.py"

Number of matches with a shift of 1= 2
Number of matches with a shift of 2= 6
Number of matches with a shift of 3= 2
Max matches with key_length of 2
```
**Finding Key**
```python
'''
Finding Key 
'''
charset = 'ab'
cipher = "bababaaaba"
possible_keys = ['aa', 'ab', 'ba', 'bb']
res = ''

for key in possible_keys:
    print(f"Current Key {key}")
    current_char_index = 0

    for cipher_char in cipher:

        res += charset[charset.find(cipher_char) -
                       charset.find(key[current_char_index])]

        current_char = key[not current_char_index]
    print(res)
    res = ''
```

**Answer**
```bash
$ python -u "/Users/wyld7k/Desktop/HPU-Files/Classes/Spring-2025/Cryptography/Labs/question-19.py"
Current Key aa
bababaaaba
Current Key ab 
bababaaaba
Current Key ba
abababbbab
Current Key bb
abababbbab
```

Section 2.9# 2, 4, 9
2. The following ciphertext was the output of a shift cipher: lcllewljazlnnzmvyiylhrmhza
    By performing a frequency count, guess the key used in the cipher. Use the computer to test your hypothesis. What is the decrypted plaintext? (The ciphertext is stored in the downloadable computer files (bit.ly/2JbcS6p) under the name lcll.)
**Shift-Decryptor**
```bash
$ cat question-2.py
import sys

charset = 'abcdefghijklmnopqrstuvwxyz'
ciphertext = 'lcllewljazlnnzmvyiylhrmhza'
key = ''
res = ''

key_length = len(key)
match_count = 0
counts = {}
'''Decrypt Shift'''

for i in range(len(charset)):
    for j in range(len(ciphertext)):
        res += charset[((charset.find(ciphertext[j]) - i) % 26)]
    print(f"Shift Amount {i} : {res}")
    res = ''

``` 

**Answer**
```bash
python -u "/Users/wyld7k/Desktop/HPU-Files/Classes/Spring-2025/Cryptography/Labs/hw-1.py" | grep 7
Shift Amount 7 : eveexpectseggsforbreakfast
```
    
4. The following ciphertext was encrypted by an affine cipher: `edsgickxhuklzveqzvkxwkzukcvuh` . The first two letters of the plaintext are if. Decrypt.
![[Pasted image 20250122112512.png]]
**Decrypt Affine**
```python
import sys

charset = 'abcdefghijklmnopqrstuvwxyz'
ciphertext = 'edsgickxhuklzveqzvkxwkzukcvuh'
res = ''
'''Decrypt Affine'''

for char in ciphertext:
    res += charset[(3*charset.find(char) + 22) % 26]
print(res)
```

```bash
$ python -u "/Users/wyld7k/Desktop/HPU-Files/Classes/Spring-2025/Cryptography/Labs/question-4.py"
ifyoucanreadthisthankateacher
```

9. 