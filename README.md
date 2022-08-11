
# Elliptic curve in PARI/GP using python

In this work, we represent how to use PARI/GP for elliptic curves with python and apply it to the use of ECDH and ECDSA.


## Content
- Introduction
- Setup
- Using PARI/GP
  - Using an elliptic curve with PARI/GP
- ECDH in python
- ECDSA in python
## Introduction

.........

## Setup

To use [PARI/GP](https://pari.math.u-bordeaux.fr/doc.html) in python, we need to use a python library named [cypari2](https://github.com/sagemath/cypari2), this library supports both Python 2 and Python 3. To install it, we use:

```bash
  pip install cypari2 [--user]
```
(the --user option allows to install cypari2 for a single user and avoids using pip with administrator rights). Depending on your operating system, the pip command can also be called pip2 or pip3.

or we can also use:

```bash
  pip install git+https://github.com/sagemath/cypari2.git [--user]
```

for the moment, we are able to use PARI/GP with python

## Using PARI/GP

To use PARI/GP, we must first create an object of the PARI() class, then use it.

```python
  from cypari2 import Pari
  pari = Pari()
```
 or also:

 ```python
  import cypari2
  pari = cypari2.Pari()
 ```
now we can use PARI/GP and for example:

```python
  # to generate a random prime from number of bits
  randomPrimeNumber = pari.randomprime(2**160)
  print("random prime number from 159 bits: ", randomPrimeNumber)

  # generate a random prime number less than 1000
  randomNumber = pari.random(1000)
  print("generate a random number: ", randomNumber)

  # check if a number is a prime number
  checkIfIsPrime = pari.isprime(randomNumber)
  print(randomNumber, "is a prime: ", checkIfIsPrime)
```

And the result of the code is :

![Logo](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiZCAopF99i0NGCjWa3gHk_xz96F0m5ju7VFf0NJFatcYttvtbARbzvxN8S-XsmxGlSewsW7znZo_Gwvr9dZpdeyUF4ILNMuVR1LgJ27oyjWJJhmmW7PLk3ffPwj24mXDkjAPrLDNPgxjZfMu3ZkCfhPQLp4i_JAq48KASlyrusPF4RHTs6gwEnneVJEA/s1600/ckech.png)

### Using an elliptic curve with PARI/GP

In the next few lines of code, we will use an elliptic curve with PARI/GP:

```python
  #declares an elliptic curve with a = 0 and b = 7 and p = 401
  ec = pari.ellinit([0, 7], 401)
  print(ec)

  #choose a random element of the curve
  randomElement = pari.random(ec)
  print(pari.lift(randomElement))

  #select a random generator of the cyclic group
  randomGenerator = pari.ellgenerators(ec)
  print("the random generator selected is: ", str(pari.lift(randomGenerator))[1: -1])

  #calculate the addition of two elements
  additon = pari.elladd(ec, generator, randomElement)
  print(pari.lift(additon))

  # calculate the multiplication by a scalar
  mul = pari.ellmul(ec, generator, 20)
  print(pari.lift(mul))

  #calculate the cardinal of the field
  card = pari.ellcard(ec)
  print(card)
```

Et le résultat de ces lignes de code est : 

![Logo](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiB1cJvsm0PWJ-ILWWQsDGPHJmyJhEvwE9P_6Mgu9lYeVQ6xe9LPZQ5hrCONuSWPLHFvCm7RzQKLgj1pXb7twFWR9eMeMC2TgTRW657dqCd8nmiBndC-8OXJDK4eVZFomCSL-OwiJjmdrZ7C2zngFPatA063E1f6OQNqqVO9D0MjhBmgU0zXFuMhStx8A/s762/curveA.png)

Because the generator is represented by [[n1, n2]] and this does not form the curve as a point (element) of the curve so we transfer it to take a formal point [n1, n2], by:

```
generate = str([[n1, n2]])[1: -1]
```

## ECDH in python
Elliptic-curve Diffie–Hellman (ECDH) is one of the most used cryptosystems for key exchange and used by "IMessage, Whatsapp, SSL, HTTPS", and in this section you can check the file ECDH.ipynb to see the full code of this algorithm.

The first thing we have to do is to check if cypari2 is already installed in the system, if not we have to install it.
### System part
In this part we use an object of the ECDHSys class and then we output the system parameters.
```python
  ecdhSyd = ECDHSys(a, b, p)
```
if we enter a=0, b=7 and p=43, the output will be :

    {'a': 0, 'b': 7, 'p': 43, 'ordre': 31, 'generator': '[32, 3]'}

![Logo](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgdWERMOjkpbkV2Uf0ayPcvJzL14LFgS2bXfyB0T6-KzsQfTqFNQsCsFp8J4JygV1NTwDzqMDSg2SC16s3YBKHM0lG5VntIU4UQ8awzuK18eLDRZIs8-mloVQtp2hLV5TSdOKvTDXFMYt51zKGwmTToN7YzXNS9jLyLM5WO_kX3gtl3E83XD8dFrbhGgg/s297/ECDHSystem.png)
### User part
In this part we will use an object of the ECDHUser class, then we will use it to generate a random private key; then we will double the chosen generator by the system class.

```python
ecdhUser = ECDHUser(sysKey)
```

Thus, between each user, we generate an encrypted key between them (in this example, the encrypted key is [2, 12]). We can now use this key for key exchange.

![Logo](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2oIh9Az0J0jh6uonZ2Z8IZl-WOHebUX3YLFZXYByyEBjV1Y5wLnV48TlM7vQQmI0v9CpiBDNNlTfxhrOozWEPAFRjwgTowrtXnB7_Kc7ycjXguXLaBGzwgmjS75zk0JuHTCre9g_H0OhEMeus_BE3blEk1qUEdat8TKZmVb-mZUq45R7b_u6DqWS1nA/s741/ECDHUser.png)

## ECDSA in python

....