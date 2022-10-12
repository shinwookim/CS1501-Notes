# Shannon's Entropy
- Measure how much data is unpredictable
- H(c)=$-1 \times \log_2{Pr(c)}$
- Pr(c) is the probability of character c
- Pr(c) = 0.5, then H(c)=1bit
- Pr(c)=0.25. then H(c)=2 bits

How can we measure the entorpy of a file? We can sum each unique character c and multiply it by the probability///

- average number of bits requied to store a chacter in that file.
Huffman compression achieve the minum/optimal, it produces a file that is equal to the entorpy of the file

How do we determine the probability of c?
- dependes on many factor(contxt of sender, receiver), world knolwedge
- Given none of the above Pr(c)= f(c)/filesize

We can also apply the concept of entropy to lanague:
Translating a langue to binary,
the entory puis the avg number of bits required to store a letter of the language.
Entr(languge) * length of msg = amount of info contained in that message

Uncompressed Eng has 0.6 and 1.3 bits of entropy per char of mesg.