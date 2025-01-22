Section 2.8# 5, 6, 15 and 19
5. Encrypt howareyou using the affine function . What is the decryption function? Check that it works
6. encrypt messages using the affine function mod 26. Decrypt the ciphertext .
15. ![[Pasted image 20250113120758.png]]
For an affine cipher to be decryptable, a must be coprime with the modulus (in this case 30) (B is just a shift), so each encrypted letter is unique.  
The numbers coprime to 30 are: 1, 7, 11, 13, 17, 19, 23, 29 (8 nums).
2. 'a' and 'd'
3. The reasoning behind this is if 10(letter1 - letter2) % 30 = 0 they will collide and both equal 0 as they're both divisible by 30 (using difference to demonstrate this relationship)
```bash
  ╱  ~/Desktop/HPU-Files/Classes/Spring-2025/Cryptography/Labs  cat affine_dups.py                                                                                                                      ✔ ╱ 12:57:22 PM 
import sys
dups = []

for i in range(int(sys.argv[1])):
    for j in range(int(sys.argv[1])):
        if not((int(sys.argv[2]) * i ) - (int(sys.argv[2]) * j) % 30) :
            dups.append((i,j))

print(dups)
  ╱  ~/Desktop/HPU-Files/Classes/Spring-2025/Cryptography/Labs  python3 affine_dups.py 30 10                                                                                                            ✔ ╱ 12:57:42 PM 
[(0, 0), (0, 3), (0, 6), (0, 9), (0, 12), (0, 15), (0, 18), (0, 21), (0, 24), (0, 27), (1, 1), (1, 4), (1, 7), (1, 10), (1, 13), (1, 16), (1, 19), (1, 22), (1, 25), (1, 28), (2, 2), (2, 5), (2, 8), (2, 11), (2, 14), (2, 17), (2, 20), (2, 23), (2, 26), (2, 29)]

```

5. Number 19th must be done by hand and show or explain your work on each problem. ![[Pasted image 20250113120833.png]]
Section 2.9# 2, 4, 9
2. The following ciphertext was the output of a shift cipher: lcllewljazlnnzmvyiylhrmhza
    By performing a frequency count, guess the key used in the cipher. Use the computer to test your hypothesis. What is the decrypted plaintext? (The ciphertext is stored in the downloadable computer files (bit.ly/2JbcS6p) under the name lcll.)
    
Number 19th must be done by hand and show or explain your work on each problem.
