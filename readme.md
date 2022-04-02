
## Install Apertium Machine Translation Plateform
- here is the link to install for unix based system -
https://wiki.apertium.org/wiki/Install_Apertium_core_using_packaging

- for other systems -  
https://wiki.apertium.org/wiki/Installation




## 1. Create the dictionaries of sumerian and English
- Define vocabulary
- Define symbols/tags for the sumerian language
- Define vocabulary along with morphology 



### **Compile the dictionaries from left to right and right to left using the following commands**

<br />
<br />

## A. for Sumerian dictionary

- provide the correct path of the dictionary 

from left to right (to generate the morphology)
``` 
lt-comp lr apertium-sum.sum.dix sum-eng.automorf.bin
```
from right to left (to generate the words)
```
lt-comp rl apertium-sum.sum.dix eng-sum.autogen.bin
```

### to test check
```
echo da-da-ni | lt-proc sum-eng.automorf.bin
```
 <br />
 <br />


## B. for english dictionary
from left to right (to generate the morphology)
``` 
lt-comp lr apertium-eng.eng.dix eng-sum.automorf.bin
```
from right to left (to generate the words)
```
lt-comp rl apertium-eng.eng.dix sum-eng.autogen.bin
```

## To test check
```
echo child | lt-proc eng-sum.automorf.bin
```
<br />
<br />


## 2. Create Bilungal Dictionary (Sumerian-English)
- provide translation of of all the words that are defined in sumerian dict
- to not get unknown all these words shold be present in an english dictionary as well

### compile dictionaries in both the directions


```
lt-comp lr apertium-sum-eng.sum-eng.dix sum-eng.autobil.bin
lt-comp rl apertium-sum-eng.sum-eng.dix eng-sum.autobil.bin

```


## 3. Write the rules and compile
- Write rules to transfer morphology from sumerian language to english

```
 apertium-preprocess-transfer apertium-sum-eng.sum-eng.t1x sum-eng.t1x.bin
```
 <br />
  <br />


This makes the basic RBMT for sumerian-english in action 
- For now only for nouns (lugal(king),iti(month),dumu(child),etc. )
-  The basic pipeline also require POS tagging system to select the correct form out of all translated, which needs to be integrated from our already built [translation pipeline](https://github.com/cdli-gh/Sumerian-Translation-Pipeline)
-  For now this is been done by simple script as mentioned in apertium-tool
```
gawk 'BEGIN{RS="$"; FS="/";}{nf=split($1,COMPONENTS,"^"); for(i = 1; i<nf; i++) printf COMPONENTS[i];
if($2 != "") printf("^%s$",$2);}'
```
 <br />
  <br />
  

# Test

To translate the word/sentence use the following combined pipeline code in unix environment

```
echo "lugal" | lt-proc sum-eng.automorf.bin | gawk 'BEGIN{RS="$"; FS="/";}{nf=split($1,COMPONENTS,"^"); for(i = 1; i<nf; i++) printf COMPONENTS[i];
if($2 != "") printf("^%s$",$2);}' | apertium-transfer apertium-sum-eng.sum-eng.t1x sum-eng.t1x.bin sum-eng.autobil.bin | lt-proc -g sum-eng.autogen.bin
```
which results as 
```
#king
```
