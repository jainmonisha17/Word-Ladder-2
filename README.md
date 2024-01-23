# Word-Ladder-2
Given two distinct words startWord and targetWord, and a list denoting wordList of unique words of equal lengths. Find all shortest transformation sequence(s) from startWord to targetWord. You can return them in any order possible.


Intuition:
The intuition behind using the BFS traversal technique for these kinds of problems is that if we notice carefully, we go on replacing the characters one by one which seems just like we’re moving level-wise in order to reach the destination i.e. the targetWord. Here, in the example below we can notice there are two possible paths in order to reach the targetWord.


Contrary to the previous problem, here we do not stop the traversal on the first occurrence of the targetWord, but rather continue it for as many occurrences of the word as possible as we need all the shortest possible sequences in order to reach the destination word. The only trick here is that we do not have to delete a particular word immediately from the wordList even if during the replacement of characters it matches with the transformed word. Instead, we delete it after the traversal for a particular level when completed which allows us to explore all possible paths. This allows us to discover multiple sequences in order to reach the targetWord involving similar words. 

From the above figure, we can configure that there can be 2 shortest possible sequences in order to reach the word ‘cog’.

Approach:
This problem uses the BFS traversal technique for finding out all the shortest possible transformation sequences by exploring all possible ways in which we can reach the targetWord.

Initial configuration:

Queue: Define a queue data structure to store the level-wise formed sequences. The queue will be storing a List of strings, which will be representing the path till now. The last word in the list will be the last converted word. 
Hash set: Create a hash set to store the elements present in the word list to carry out the search and delete operations in O(1) time. 
Vector: Define a 1D vector ‘usedOnLevel’ to store the words which are currently being used for transformation on a particular level and a 2D vector ‘ans’ for storing all the shortest sequences of transformation.
The Algorithm for this problem involves the following steps:

Firstly, we start by creating a hash set to store all the elements present in the wordList which would make the search and delete operations faster for us to implement.
Next, we create a Queue data structure for storing the successive sequences/ path in the form of a vector which on transformation would lead us to the target word.
Now, we add the startWord to the queue as a List and also push it into the usedOnLevel vector to denote that this word is currently being used for transformation in this particular level.
Pop the first element out of the queue and carry out the BFS traversal, where for each word that popped out from the back of the sequence present at the top of the queue, we check for all of its characters by replacing them with ‘a’ – ‘z’ if they are present in the wordList or not. In case a word is present in the wordList, we simply first push it onto the usedOnLevel vector and do not delete it from the wordList immediately.
Now, push that word into the vector containing the previous sequence and add it to the queue. So we will get a new path, but we need to explore other paths as well, so pop the word out of the list to explore other paths.
After completion of traversal on a particular level, we can now delete all the words that were currently being used on that level from the usedOnLevel vector which ensures that these words won’t be used again in the future, as using them in the later stages will mean that it won’t be the shortest path anymore.
If at any point in time we find out that the last word in the sequence present at the top of the queue is equal to the target word, we simply push the sequence into the resultant vector if the resultant vector ‘ans’ is empty.
If the vector is not empty, we check if the current sequence length is equal to the first element added in the ans vector or not. This has to be checked because we need the shortest possible transformation sequences.
In case, there is no transformation sequence possible, we return an empty 2D vector.


import java.util.*;
import java.lang.*;
import java.io.*;

// A comparator function to sort the answer.
class comp implements Comparator < ArrayList < String >> {

    public int compare(ArrayList < String > a, ArrayList < String > b) {
        String x = "";
        String y = "";
        for (int i = 0; i < a.size(); i++)
            x += a.get(i);
        for (int i = 0; i < b.size(); i++)
            y += b.get(i);
        return x.compareTo(y);
    }
}

public class Main {

    public static void main(String[] args) throws IOException {
        String startWord = "der", targetWord = "dfs";
        String[] wordList = {
            "des",
            "der",
            "dfr",
            "dgt",
            "dfs"
        };

        wordLadderTwo obj = new wordLadderTwo();
        ArrayList < ArrayList < String >> ans = obj.findSequences(startWord, targetWord, wordList);
        
        // If no transformation sequence is possible.
        if (ans.size() == 0)
            System.out.println(-1);
        else {

            Collections.sort(ans, new comp());
            for (int i = 0; i < ans.size(); i++) {
                for (int j = 0; j < ans.get(i).size(); j++) {
                    System.out.print(ans.get(i).get(j) + " ");
                }
                System.out.println();
            }
        }
    }
}

public class wordLadderTwo {
    public ArrayList<ArrayList<String>> findSequences(String startWord, String targetWord, String[] wordList) {
        // push all values of wordList into a set
        // to make deletion from it easier and in less time complexity

        HashSet<String> myHashSet = new HashSet<String>();
        int len = wordList.length;
        for(int i = 0; i < len; i++) {
            myHashSet.add(wordList[i]);
        }

        // creating a queue ds which stores the words in a sequence which is 
        // required to reach the target word after successive transformations
        Queue<ArrayList<String>> queue = new LinkedList<>();
        ArrayList<String> arrayList = new ArrayList<>();
        arrayList.add(startWord);
        queue.add(arrayList);
        ArrayList<String> usedOnLevel = new ArrayList<>();
        usedOnLevel.add(startWord);
        int level = 0;

        // A vector to store the resultant transformation sequence.
        // to store the final ans
        ArrayList<ArrayList<String>> ans = new ArrayList<>();
        int count = 0;

        // BFS traversal with pushing the new formed sequence in queue
        // when after a transformantin, a word is found in wordlist

        while(!queue.isEmpty()) {
            count++;
            ArrayList<String> vec = queue.peek();
            queue.remove();

            // now, erase all words that have been
            // used in the previous levels to transform
            if(vec.size() > level) {
                level++;
                for(String it: usedOnLevel) {
                    myHashSet.remove(it);
                }
            } 

            String word = vec.get(vec.size() - 1);

            // store the answers if the end word matches with the target word
            if(word.equals(targetWord)) {
                // first sequence where we reached the end
                if(ans.size() == 0) {
                    ans.add(vec);
                } else if (ans.get(0).size() == vec.size()) {
                    ans.add(vec);
                }
            }

            for(int i = 0; i < word.length(); i++) {
                // now replace each character of 'word' with char
                // from a-z then check if 'word' exists in wordList
                for(char c = 'a'; c <= 'z'; c++) {
                    char replacedCharArray[] = word.toCharArray();
                    replacedCharArray[i] = c;
                    String replacedWord = new String(replacedCharArray);

                    if(myHashSet.contains(replacedWord) == true) {
                        vec.add(replacedWord);

                        // java works by reference, so enter the copy of vec
                        // otherwise if you remove word from vec in next lines, it will 
                        // remove from everywhere

                        ArrayList<String> temp = new ArrayList<>(vec);
                        queue.add(temp);

                        // marks as visited on the level
                        usedOnLevel.add(replacedWord);
                        vec.remove(vec.size() - 1);
                    }
                }
            }
        }
        return ans;
    } 
}

