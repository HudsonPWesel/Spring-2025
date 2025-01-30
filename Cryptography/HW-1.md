## Section 2.8# 5, 6, 15 and 19
5. Encrypt howareyou using the affine function 5x + 7 (mod 26). What is the decryption function? Check that it works.
**affine-function.py**
```python
charset = 'abcdefghijklmnopqrstuvwxyz'
plaintext = 'howareyou'
res = ''

for chr in plaintext:
    res += charset[(5 * (charset.find(chr)) + 7) % 26]
print(res)
```

**Answer PT .1**

```bash
$ python3 affine-function.py

qznhobxzd
```

**mutliplicative_finder.py**
```python
import sys;
charset='abcdefghijklmnopqrstuvwxyz'


global multiplicative_inverse
multiplicative_inverse=-1

print(f"Usage arg1=m,arg2=mod-value,arg3=remainder-val")
for i in range(len(charset)):
    multiplicative_inverse = i if (int(sys.argv[1])*i)%int(sys.argv[2]) == int(sys.argv[3]) else multiplicative_inverse

if multiplicative_inverse == -1:
    print('multiplicative_inverse not found')
print(multiplicative_inverse)

```
**Finding Decryption Function**

```bash
python3 multiplicative_finder.py 5 26 1                                                                  
Usage arg1=m,arg2=mod-value,arg3=remainder-val
21
```

**affine_decryptor.py**
```python
import sys
charset = 'abcdefghijklmnopqrstuvwxyz'

ciphertext, multiplicative_inverse = sys.argv[1], sys.argv[2] # Passing in the ciphertext AND the multiplicative inverse 
print(ciphertext, multiplicative_inverse)

plaintext = ''

print("Make Sure you've defined your decript function in conjunction with the multiplicative_inverse")


def decrypt_char(char, multiplicative_inverse):
    return charset[(int(multiplicative_inverse) * (charset.find(char) - 7)) % 26] # predfined from 5x + 7 = y --> 5x = y - 7 --> x = 21(y - 7)


for char in ciphertext:
    letter = decrypt_char(char, multiplicative_inverse)
    plaintext += letter
    print(f"Found ClearText Letter: {letter}")


print("plaintext=", plaintext)

```
**Answer**
```bash
python3 affine_cipher.py qznhobxzd 21                                                                          
qznhobxzd 21
Make Sure you've defined your decript function in conjunction with the multiplicative_inverse
Found ClearText Letter: h
Found ClearText Letter: o
Found ClearText Letter: w
Found ClearText Letter: a
Found ClearText Letter: r
Found ClearText Letter: e
Found ClearText Letter: y
Found ClearText Letter: o
Found ClearText Letter: u
plaintext= howareyou
```


6. **You encrypt messages using the affine function mod 26. Decrypt the ciphertext *GM***
**Finding Decryption Function**
*9x + 2 = y*
*9x = y -2*
*x = 3(y - 2)*

**affine_decryptor.py**
\<SNIP\> *means that text was removed for sake of brevity*
```python
<SNIP>

def decrypt_char(char, multiplicative_inverse):
    return charset[(int(multiplicative_inverse) * (charset.find(char) - 2)) % 26] # Changed function to match current problem 3(y - 2)

<SNIP>
```

**Answer**
```bash
python3 affine_cipher.py gm 3                                                                      
gm 3
Make Sure you've defined your decript function in conjunction with the multiplicative_inverse
Found ClearText Letter: m
Found ClearText Letter: e
plaintext= me
```
15. 
	a. For an affine cipher to be precisely decryptable, *a* must be coprime with the modulus (in this case 30) (B is just a shift), so each encrypted letter is unique. The numbers coprime to 30 are: 1, 7, 11, 13, 17, 19, 23, 29 (8 nums).
	b. 'a' and 'd'
		c. The reasoning behind this is if 10(letter1 - letter2) % 30 = 0 they will collide and both equal 0 as they're both divisible by 30 (using difference to demonstrate this relationship)
**affine_dups.py**
```python
$ cat affine_dups.py

import sys
dups = []

for i in range(int(sys.argv[1])):
    for j in range(int(sys.argv[1])):
        if not((int(sys.argv[2]) * i ) - (int(sys.argv[2]) * j) % 30) :
            dups.append((i,j))
print(dups)

```

**Answer**
```bash
$ python3 affine_dups.py 30 10
 
[(0, 0), (0, 3), (0, 6), (0, 9), (0, 12), (0, 15), (0, 18), (0, 21), (0, 24), (0, 27), (1, 1), (1, 4), (1, 7), (1, 10), (1, 13), (1, 16), (1, 19), (1, 22), (1, 25), (1, 28), (2, 2), (2, 5), (2, 8), (2, 11), (2, 14), (2, 17), (2, 20), (2, 23), (2, 26), (2, 29)]
```

19. *Suppose there is a language that has only the letters and . The frequency of the letter is .1 and the frequency of is .9. A message is encrypted using a Vigenère cipher (working mod 2 instead of mod 26). The ciphertext is BABABAAABA. The key length is 1, 2, or 3.*
a. )
**key-length-finder.py**
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

**Answer**
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
*using **ab** looks like it makes the most*
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

## Section 2.9# 2, 4, 9
*Question 2*
2. The following ciphertext was the output of a shift cipher: lcllewljazlnnzmvyiylhrmhza
    By performing a frequency count, guess the key used in the cipher. Use the computer to test your hypothesis. What is the decrypted plaintext? (The ciphertext is stored in the downloadable computer files (bit.ly/2JbcS6p) under the name lcll.)
**shift-decryptor.py**
```bash
$ cat shift-decryptor.py
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
**affine-decryptor.py**
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

**Answer**
```bash
$ python -u "/Users/wyld7k/Desktop/HPU-Files/Classes/Spring-2025/Cryptography/Labs/question-4.py"
ifyoucanreadthisthankateacher
```

**crunch**
*This will generate all combos of chars with len of 6*
```bash
crunch 6 6 -t @@@@@@ | tee -a all-char-combos.txt
<SNIP>
holmes
<SNIP>
```
**vigenere-decryptor.py**
```python
import math

cipher = 'ocwyikoooniwugpmxwktzdwgtssayjzwyemdlbnqaaavsuwdvbrflauplooubfgqhgcscmgzlatoedcsdeidpbhtmuovpiekifpimfnoamvlpqfxejsmxmpgkccaykwfzpyuavtelwhrhmwkbbvgtguvtefjlodfefkvpxsgrsorvgtajbsauhzrzalkwuowhgedefnswmrciwcpaaavogpdnfpktdbalsisurlnpsjyeatcuceesohhdarkhwotikbroqrdfmzghgucebvgwcdqxgpbgqwlpbdaylooqdmuhbdqgmyweuik'

general_frequencies = ["e", "t", "a", "o", "i", "n", "s", "h", "r", "d", "l",
                       "c", "u", "m", "w", "f", "g", "y", "p", "b", "v", "k", "j", "x", "q", "z"]
charset = 'abcdefghijklmnopqrstuvwxyz'
res = [''] * (len(cipher))
key = ''


def count_chars():
    vals = (sorted({letter: cipher.count(letter)
                    for letter in cipher}.items(), key=lambda x: x[1], reverse=True))
    print(vals)
    return vals
    # for val in vals:
    #     print(val)


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
        # print("Number of matches with a shift of "+str(shift)+"= "+str(count))
        current_max = (shift, count) if count > current_max[1] else current_max
    print(f"Max matches with key_length of {current_max[0]}")
    return current_max[0]


def shift_by_nth_freq_letter(nth_frequent_letter, key_length):
    '''
    Find which letters I"m going to shift
    Calc shift
    Shift perform by key size
    Insert into correction position
    # Given the frequency (0 = most frequent), takes all coresponding letters (0,6,12,etc) and shifts them by the difference between the nth freqnt letter in the cipher vs nth freqnt letter in the english dictinoary  
    '''
    letter_frequencies = count_chars()

    insert_index = nth_frequent_letter
    shift_amount = (charset.find(letter_frequencies[nth_frequent_letter][0]) -
                    charset.find(general_frequencies[nth_frequent_letter])) % 26
    global key
    key += general_frequencies[nth_frequent_letter]

    while insert_index < len(cipher):
        # Could also just +
        shifted_letter = charset[(insert_index + shift_amount) % 26]
        # Most freq letter = a (0) - e (4) = shift = -4 # index of nth frequent letter from cipher text in charset - the index of nth frequent letter from english in charset
        res[insert_index] = shifted_letter
        insert_index += 6

    print(f"Used Key {key}")
    print(res)


def decrypt():
    # Create empty string to insert shifted chars

    letter_frequencies = count_chars()
    key_length = find_key_length(10)
    for i in range(key_length):
        # 0 INDEXED (most freq letter = 0)
        shift_by_nth_freq_letter(i, key_length)

    print(str.join('', res))


# decrypt()


def decrypt_affine(known_key):
    '''
   Shift each char using the known key 
    '''
    insert_index = key_index = 0
    # home 7
    while insert_index < len(cipher):
        new_letter_index = (charset.find(
            cipher[insert_index]) - charset.find(known_key[key_index])) % 26
        shifted_letter = charset[new_letter_index]
        res[insert_index] = shifted_letter
        insert_index += 1
        key_index = key_index + 1 if key_index + 1 < len(known_key) else 0
    print(f"Used Key {known_key}")
    print(res)
    print(str.join('', res))

find_key_length(12) # Finds key length
decrypt_affine('holmes') # Decrypts
```

**Answer Revealed**
```bash
python -u "/Users/wyld7k/Desktop/HPU-Files/Classes/Spring-2025/Cryptography/Labs/question-9.py"
Shift 1 Matches : 13
Shift 2 Matches : 13
Shift 3 Matches : 11
Shift 4 Matches : 6
Shift 5 Matches : 15
Shift 6 Matches : 23
Shift 7 Matches : 8
Shift 8 Matches : 5
Shift 9 Matches : 12
Shift 10 Matches : 11
Shift 11 Matches : 10
Shift 12 Matches : 18

Used Key holmes
['h', 'o', 'l', 'm', 'e', 's', 'h', 'a', 'd', 'b', 'e', 'e', 'n', 's', 'e', 'a', 't', 'e', 'd', 'f', 'o', 'r', 's', 'o', 'm', 'e', 'h', 'o', 'u', 'r', 's', 'i', 'n', 's', 'i', 'l', 'e', 'n', 'c', 'e', 'w', 'i', 't', 'h', 'h', 'i', 's', 'l', 'o', 'n', 'g', 't', 'h', 'i', 'n', 'b', 'a', 'c', 'k', 'c', 'u', 'r', 'v', 'e', 'd', 'o', 'v', 'e', 'r', 'a', 'c', 'h', 'e', 'm', 'i', 'c', 'a', 'l', 'v', 'e', 's', 's', 'e', 'l', 'i', 'n', 'w', 'h', 'i', 'c', 'h', 'h', 'e', 'w', 'a', 's', 'b', 'r', 'e', 'w', 'i', 'n', 'g', 'a', 'p', 'a', 'r', 't', 'i', 'c', 'u', 'l', 'a', 'r', 'l', 'y', 'm', 'a', 'l', 'o', 'd', 'o', 'r', 'o', 'u', 's', 'p', 'r', 'o', 'd', 'u', 'c', 't', 'h', 'i', 's', 'h', 'e', 'a', 'd', 'w', 'a', 's', 's', 'u', 'n', 'k', 'u', 'p', 'o', 'n', 'h', 'i', 's', 'b', 'r', 'e', 'a', 's', 't', 'a', 'n', 'd', 'h', 'e', 'l', 'o', 'o', 'k', 'e', 'd', 'f', 'r', 'o', 'm', 'm', 'y', 'p', 'o', 'i', 'n', 't', 'o', 'f', 'v', 'i', 'e', 'w', 'l', 'i', 'k', 'e', 'a', 's', 't', 'r', 'a', 'n', 'g', 'e', 'l', 'a', 'n', 'k', 'b', 'i', 'r', 'd', 'w', 'i', 't', 'h', 'd', 'u', 'l', 'l', 'g', 'r', 'e', 'y', 'p', 'l', 'u', 'm', 'a', 'g', 'e', 'a', 'n', 'd', 'a', 'b', 'l', 'a', 'c', 'k', 't', 'o', 'p', 'k', 'n', 'o', 't', 's', 'o', 'w', 'a', 't', 's', 'o', 'n', 's', 'a', 'i', 'd', 'h', 'e', 's', 'u', 'd', 'd', 'e', 'n', 'l', 'y', 'y', 'o', 'u', 'd', 'o', 'n', 'o', 't', 'p', 'r', 'o', 'p', 'o', 's', 'e', 't', 'o', 'i', 'n', 'v', 'e', 's', 't', 'i', 'n', 's', 'o', 'u', 't', 'h', 'a', 'f', 'r', 'i', 'c', 'a', 'n', 's', 'e', 'c', 'u', 'r', 'i', 't', 'i', 'e', 's']

holmeshadbeenseatedforsomehoursinsilencewithhislongthinbackcurvedoverachemicalvesselinwhichhewasbrewingaparticularlymalodorousproducthisheadwassunkuponhisbreastandhelookedfrommypointofviewlikeastrangelankbirdwithdullgreyplumageandablacktopknotsowatsonsaidhesuddenlyyoudonotproposetoinvestinsouthafricansecurities

```