package com.example.interview;

import com.gs.collections.api.multimap.MutableMultimap;

import java.io.*;
import java.util.*;
import java.util.List;

public class AnagramSolution
{
    public List<HashMap<Character, Integer>> getCharsCountMap(String word)
    {
        List<HashMap<Character, Integer>> result = new ArrayList<>();
        char[] wordToChars =  word.toCharArray();
        for(char c : wordToChars)
        {
            boolean existFlag = false;
            for(HashMap<Character, Integer> countMap : result)
            {
                if(countMap.get(c) != null)
                {
                    countMap.put(c, countMap.get(c) + 1);
                    existFlag = true;
                }
            }
            if(!existFlag)
            {
                HashMap<Character, Integer> cMap = new HashMap<>();
                cMap.put(c, 1);
                result.add(cMap);
            }
        }
            return result;
    }

    public boolean contain(List<HashMap<Character, Integer>> candidateCharsCountMap, List<HashMap<Character, Integer>> inputWordToCharsCountMap)
    {
        boolean subsetFlag = false;

        if(candidateCharsCountMap.size() > inputWordToCharsCountMap.size())
            return false;

        for(HashMap<Character, Integer> candidate : candidateCharsCountMap)
        {
            Map.Entry<Character,Integer> entry = candidate.entrySet().iterator().next();
            Character key= entry.getKey();
            Integer value=entry.getValue();

            for(Map<Character, Integer> inputWord : inputWordToCharsCountMap)
            {
                if(inputWord.containsKey(key) && inputWord.get(key)>=value)
                {
                    subsetFlag = true;
                    break;
                }
                subsetFlag = false;
            }
            if(!subsetFlag)
                return false;
        }
        return subsetFlag;
    }

    public List<HashMap<Character, Integer>> subtract(List<HashMap<Character, Integer>> candidate, List<HashMap<Character, Integer>> inputWord)
    {
        for(HashMap<Character, Integer> c : candidate)
        {
            Map.Entry<Character,Integer> entry = c.entrySet().iterator().next();
            Character key= entry.getKey();
            Integer value=entry.getValue();
            for(HashMap<Character, Integer> i : inputWord)
            {
                if(i.containsKey(key)){
                    if(i.get(key) == value)
                        inputWord.remove(i);
                    else
                        i.put(key, i.get(key) - value);
                }
            }
        }
        return inputWord;
    }

    public List<HashMap<Character, Integer>> add(List<HashMap<Character, Integer>> candidate, List<HashMap<Character, Integer>> inputWord)
    {
        for(HashMap<Character, Integer> c : candidate)
        {
            Map.Entry<Character,Integer> entry = c.entrySet().iterator().next();
            Character key= entry.getKey();
            Integer value=entry.getValue();
            for(HashMap<Character, Integer> i : inputWord)
            {
                if(i.get(key) != null)
                    i.put(key, i.get(key)+value);
                else
                {
                    Map<Character, Integer> map = new HashMap<>();
                    map.put(key, value);
                }
            }
        }
        return inputWord;
    }

    public List<List<String>> search(String inputWord)
    {
        String filePath = "D:\\workspace\\simple-service-webapp\\src\\main\\java\\com\\example\\interview\\words.txt";
        List<String> wordLists = new ArrayList<>();
        List<String> anagrams = new ArrayList();
        List<List<String>> allAnagrams = new ArrayList<>();

        try
        {
            String word;
            BufferedReader reader =new BufferedReader(new FileReader(filePath));
            List<HashMap<Character, Integer>> inputWordToCharsCountMap = getCharsCountMap(inputWord);
            while((word = reader.readLine()) != null)
            {
                List<HashMap<Character, Integer>> candidateCharsCountMap = getCharsCountMap(word);
                if(word.length() != 1 && contain(candidateCharsCountMap, inputWordToCharsCountMap))
                    wordLists.add(word);
            }

            getSubAnagram(wordLists, inputWordToCharsCountMap, anagrams, allAnagrams);
        }
        catch (FileNotFoundException e)
        {
            e.printStackTrace();
        }
        catch (IOException e)
        {
            e.printStackTrace();
        }
        return allAnagrams;
    }

    public List<List<String>> getSubAnagram(List<String> wordLists, List<HashMap<Character, Integer>> inputWord,
                                            List<String> anagrams, List<List<String>> allAnagrams)
    {
        if(inputWord.isEmpty())
            allAnagrams.add(anagrams);
        else
        {
            for(String word : wordLists)
            {
             List<HashMap<Character, Integer>> candidate = getCharsCountMap(word);
               if(candidate.size() <= inputWord.size() && contain(candidate, inputWord))
               {
                   inputWord = subtract(candidate, inputWord);
                   anagrams.add(word);
                   getSubAnagram(wordLists, inputWord, anagrams, allAnagrams);
                   inputWord = add(candidate, inputWord);
               }
            }
        }
        return allAnagrams;
    }

    public void printTwoWords(List<List<String>> allAnagrams)
    {
        boolean existTowWords = false;
        if(!allAnagrams.isEmpty())
        {
            for(List<String> anagrams : allAnagrams)
            {
                if(anagrams.size() == 2)
                {
                    existTowWords = true;
                    System.out.println("The two anagrams are: ");
                    for(String anagram : anagrams)
                    {
                        System.out.println(anagram);
                    }
                }
            }
        }

        if(!allAnagrams.isEmpty() || !existTowWords)
            System.out.println("No two words anagram");
    }

    public static void printMostWords(List<List<String>> allAnagrams)
    {
        List<String> maxAnagrams = new ArrayList<>();
        if(!allAnagrams.isEmpty())
        {
            for(List<String> anagrams : allAnagrams)
            {
                if(maxAnagrams.size() < anagrams.size())
                    maxAnagrams = anagrams;
            }

            System.out.println("The most number of words are: ");
            for(String anagram : maxAnagrams)
            {
                System.out.println(anagram);
            }
        }
    }

    public static void main(String[] args)
    {
        String testString = "incredible";

        AnagramSolution as = new AnagramSolution();
        List<List<String>> allAnagrams = as.search(testString);
        as.printTwoWords(allAnagrams);
        as.printMostWords(allAnagrams);
    }
}
