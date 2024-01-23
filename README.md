# Word-Ladder-2
Given two distinct words startWord and targetWord, and a list denoting wordList of unique words of equal lengths. Find all shortest transformation sequence(s) from startWord to targetWord. You can return them in any order possible.


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

