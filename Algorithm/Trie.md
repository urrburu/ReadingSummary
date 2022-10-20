## What is Trie?

> Trie is a type of k-ary search tree used for storing and searching a specific key from a set. Using Trie, search complexities can be brought to optimal limit (key length). 

If we store keys in a [binary search tree](https://www.geeksforgeeks.org/binary-search-tree-data-structure/), a well balanced BST will need time proportional to **M * log N**, where **M** is the maximum string length and **N** is the number of keys in the tree. Using Trie, the key can be searched in **O(M)** time. However, the penalty is on Trie storage requirements (Please refer to [Applications of Trie](https://www.geeksforgeeks.org/advantages-trie-data-structure/) for more details).

Trie is also known as **digital tree** or **prefix tree**. Refer to [**this**](https://www.geeksforgeeks.org/introduction-to-trie-data-structure-and-algorithm-tutorials/) article for more detailed information.

![Trie data structure](https://media.geeksforgeeks.org/wp-content/uploads/20220828232752/Triedatastructure1.png)

Trie data structure

## Structure of Trie node:

Every node of Trie consists of multiple branches. Each branch represents a possible character of keys. Mark the last node of every key as the end of the word node. A Trie node field **isEndOfWord** is used to distinguish the node as the end of the word node. 

A simple structure to represent nodes of the English alphabet can be as follows.

-   C++
```C++
// Trie node
struct TrieNode {
     struct TrieNode *children[ALPHABET_SIZE];
     // isEndOfWord is true if the node
     // represents end of a word
     bool isEndOfWord;
}; 
```

## Insert Operation in [Trie](https://www.geeksforgeeks.org/introduction-to-trie-data-structure-and-algorithm-tutorials/):

Inserting a key into Trie is a simple approach. 

-   Every character of the input key is inserted as an individual Trie node. Note that the **children** is an array of pointers (or references) to next-level trie nodes. 
-   The key character acts as an index to the array **children**. 
-   If the input key is new or an extension of the existing key, construct non-existing nodes of the key, and mark the end of the word for the last node. 
-   If the input key is a prefix of the existing key in Trie, Simply mark the last node of the key as the end of a word. 

The key length determines Trie depth. 

The following picture explains the construction of trie using keys given in the example below.

![Insertion operation](https://media.geeksforgeeks.org/wp-content/uploads/20220902035030/ex1.png)

Insertion operation

## Search Operation in [Trie](https://www.geeksforgeeks.org/introduction-to-trie-data-structure-and-algorithm-tutorials/):

Searching for a key is similar to the insert operation. However, It only **compares the characters and moves down**. The search can terminate due to the end of a string or lack of key in the trie. 

-   In the former case, if the **isEndofWord** field of the last node is true, then the key exists in the trie. 
-   In the second case, the search terminates without examining all the characters of the key, since the key is not present in the trie.

![Searching in Trie](https://media.geeksforgeeks.org/wp-content/uploads/20220831073313/search1.png)

Searching in Trie

**Note:** Insert and search costs **O(key_length)**, however, the memory requirements of Trie is **O(ALPHABET_SIZE * key_length * N)** where N is the number of keys in Trie. There are efficient representations of trie nodes (e.g. compressed trie, [ternary search tree](http://en.wikipedia.org/wiki/Ternary_search_tree), etc.) to minimize the memory requirements of the trie.

## How to implement a Trie Data Structure?

-   Create a root node with the help of **TrieNode()** constructor.
-   Store a collection of strings that have to be inserted in the trie in a vector of strings say, **arr**.
-   Inserting all strings in Trie with the help of the **insert()** function,
-   Search strings with the help of **search()** function.

Below is the implementation of the above approach:

-   C++
```C++
// C++ implementation of search and insert
// operations on Trie
#include <bits/stdc++.h>
using namespace std;

const int ALPHABET_SIZE = 26;
// trie node
struct TrieNode
{
    struct TrieNode *children[ALPHABET_SIZE];
    // isEndOfWord is true if the node represents
    // end of a word
    bool isEndOfWord;
};

// Returns new trie node (initialized to NULLs)
struct TrieNode *getNode(void)
{
    struct TrieNode *pNode = new TrieNode;
    pNode->isEndOfWord = false;
    for(int i = 0; i < ALPHABET_SIZE; i++)
        pNode->children[i] = NULL;
    return pNode;
}

// If not present, inserts key into trie
// If the key is prefix of trie node, just
// marks leaf node

void insert(struct TrieNode *root, string key){

    struct TrieNode *pCrawl = root;
    for(int i = 0; i < key.length(); i++)
    {
		int index = key[i] -'a';
        if (!pCrawl->children[index])

            `pCrawl->children[index] = getNode();`

        `pCrawl = pCrawl->children[index];`

    `}`

    `// mark last node as leaf`

    `pCrawl->isEndOfWord =` `true``;`

`}`

`// Returns true if key presents in trie, else`

`// false`

`bool` `search(``struct` `TrieNode *root, string key)`

`{`

    `struct` `TrieNode *pCrawl = root;`

    `for` `(``int` `i = 0; i < key.length(); i++)`

    `{`

        `int` `index = key[i] -` `'a'``;`

        `if` `(!pCrawl->children[index])`

            `return` `false``;`

        `pCrawl = pCrawl->children[index];`

    `}`

    `return` `(pCrawl->isEndOfWord);`

`}`

`// Driver`

`int` `main()`

`{`

    `// Input keys (use only 'a' through 'z'`

    `// and lower case)`

    `string keys[] = {``"the"``,` `"a"``,` `"there"``,`

                    `"answer"``,` `"any"``,` `"by"``,`

                     `"bye"``,` `"their"` `};`

    `int` `n =` `sizeof``(keys)/``sizeof``(keys[0]);`

    `struct` `TrieNode *root = getNode();`

    `// Construct trie`

    `for` `(``int` `i = 0; i < n; i++)`

        `insert(root, keys[i]);`

    `// Search for different keys`

    char output[][32] = {"Not present in trie","Present in trie"};

    // Search for different keys

    cout<<"the"<<" --- "<<output[search(root, "the")]<<endl;
	cout<<"these"<<" --- "<<output[search(root, "these")]<<endl;
    cout<<"their"<<" --- "<<output[search(root, "their")]<<endl;

    `cout<<``"thaw"``<<``" --- "``<<output[search(root,` `"thaw"``)]<<endl;`

    `return` `0;`

`}`
```

**Output**

the --- Present in trie
these --- Not present in trie
their --- Present in trie
thaw --- Not present in trie

## Complexity Analysis of Trie Data Structure:

|Operation|Time Complexity|Auxiliary Space|
|---|---|---|
|Insertion|O(n)|O(n*m)|
|Searching|O(n)|O(1)|


**Related Articles:** 

-   [Trie Delete](https://www.geeksforgeeks.org/trie-delete/)
-   [Trie data structure](https://www.geeksforgeeks.org/introduction-to-trie-data-structure-and-algorithm-tutorials/)
-   [Displaying content of Trie](https://www.geeksforgeeks.org/trie-display-content/)
-   [Applications of Trie](https://www.geeksforgeeks.org/advantages-trie-data-structure/)
-   [Auto-complete feature using Trie](https://www.geeksforgeeks.org/auto-complete-feature-using-trie/)
-   [Minimum Word Break](https://www.geeksforgeeks.org/minimum-word-break/)
-   [Sorting array of strings (or words) using Trie](https://www.geeksforgeeks.org/sorting-array-strings-words-using-trie/)
-   [Pattern Searching using a Trie of all Suffixes](https://www.geeksforgeeks.org/pattern-searching-using-trie-suffixes/)

**Practice Problems:** 

1.  [Trie Search and Insert](https://practice.geeksforgeeks.org/problems/trie-insert-and-search/0)
2.  [Trie Delete](https://practice.geeksforgeeks.org/problems/trie-delete/1)
3.  [Unique rows in a binary matrix](https://practice.geeksforgeeks.org/problems/unique-rows-in-boolean-matrix/1)
4.  [Count of distinct substrings](https://practice.geeksforgeeks.org/problems/count-of-distinct-substrings/1)
5.  [Word Boggle](https://practice.geeksforgeeks.org/problems/word-boggle/0)

[**Recent Articles on Trie**](https://www.geeksforgeeks.org/tag/trie/)  
This article is contributed by [**Venki**](http://www.linkedin.com/in/ramanawithu). Please write comments if you find anything incorrect, or you want to share more information about the topic discussed above.