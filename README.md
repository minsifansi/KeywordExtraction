# Keyword Extraction in Java

Implementation of serveral algorithms for keyword extraction,including TextRank,TF-IDF,TextRank along with TFTF-IDF.Cutting words and filtering stop words are relied on [HanLP](https://github.com/hankcs/HanLP)

The repository mainly consists of three parts:

**1. Algorithm**: implementation of serveral algorithms for keyword exraction,including TextRank,TF-IDF and combining of  TextRank and TF-IDF

**2.Evaluate:**the method to evaluate the result of the algorithm,currently only the F1 Score is available

**3.Parse Documents:**methods provided to read the contens of the corpus used for test


More details can be found in [this passage](http://wulc.me/2016/05/28/%E5%85%B3%E9%94%AE%E8%AF%8D%E6%8A%BD%E5%8F%96%E7%AE%97%E6%B3%95%E7%9A%84%E7%A0%94%E7%A9%B6/)

## 1. Algorithm

### 1.1 TextRank

Source File: `TexkRank.java`

With title and content of a document as input,return 5 keywords of the documents.For example

```java
String title = "关键词抽取";
String content = "关键词自动提取是一种识别有意义且具有代表性片段或词汇的自动化技术。关键词自动提取在文本挖掘域被称为关键词抽取，在计算语言学领域通常着眼于术语自动识别，在信息检索领域，就是指自动标引。";
System.out.println(TextRank.getKeyword(title, content));

// Output: [自动, 领域, 关键词, 提取, 抽取]
```

You can change the number of keywords and the size of co-occur window ,whose default values are 5 and 3,respectively.For example:
```java
TextRank.setKeywordNumber(6);
TextRank.setWindowSize(4);
String title = "关键词抽取";
String content = "关键词自动提取是一种识别有意义且具有代表性片段或词汇的自动化技术。关键词自动提取在文本挖掘域被称为关键词抽取，在计算语言学领域通常着眼于术语自动识别，在信息检索领域，就是指自动标引。";
System.out.println(TextRank.getKeyword(title, content));
// Output:[自动, 关键词, 领域, 提取, 抽取, 自动识别]
```

From the output you can see clearly the number of keywords has change due to `TextRank.setKeywordNumber(6);`,and the size of co-occur window is not visible in the result but will affect the resutlt if you are aware of the principle of TextRank algorithm.


### 1.2 TF-IDF

Source File: `TFIDF.java`

TF-IDF algorithm is to extract the keywords of a corpus, that is, it will extract keywords for multiple documents at the same time.For example:

```java
String dir = "G:/corpusMini";
Map<String,List<String>> result= TFIDF.getKeywords(dir);
System.out.println(result);
```

Output is like this(suppose there are only 3 documents in the directory):
```
{
G:/corpusMini/00001.xml=[曹奔, 周某, 民警, 摩托车, 禁区],
G:/corpusMini/00002.xml=[翁刚, 吴红娟, 婆婆, 分割, 丈夫], 
G:/corpusMini/00003.xml=[止痛片, 伤肾, 医生, 肾衰竭, 胡婆婆]
}
```

The default number of keywords to extract is 5,you can change it by yourself,for example:

```java
String dir = "G:/corpusMini";
TFIDF.setKeywordsNumber(3);
Map<String,List<String>> result= TFIDF.getKeywords(dir);
System.out.println(result);
```

and the output will be like this
```
{
G:/corpusMini/00001.xml=[曹奔, 周某, 民警],
G:/corpusMini/00002.xml=[翁刚, 吴红娟, 婆婆], 
G:/corpusMini/00003.xml=[止痛片, 伤肾, 医生]
}
```

**To be able to run the above code, remember to specify how to read the content of file under the directory in the `ReadFile.java `, the following part `3.2 ReadFile` will explain the details about this**


### 1.3 TextRank With Multiple Window

Source File: `TextRankWithMultiWin.java`

`TextRank`  algorithm is classical, and this algorithm `TextRank With Multiple Window` is based on TextRank.

As we know, co-occurance window is an uncertain parameter in TextRank, currently we have no way to find the best co-occurance window for a document, therefore this co-occurance window will integrate the resutls generated by several co-occurance windowd by summing up the score that they generate.

Sample code is like this:
```java
String title = "关键词抽取";
String content = "关键词自动提取是一种识别有意义且具有代表性片段或词汇的自动化技术。关键词自动提取在文本挖掘域被称为关键词抽取，在计算语言学领域通常着眼于术语自动识别，在信息检索领域，就是指自动标引。";
int miniWindow = 3, maxWindow = 5;
List<String> keywords = TextRankWithMultiWin.integrateMultiWindow(title, content, miniWindow, maxWindow);
System.out.println(keywords);

/*Output*/
[自动, 关键词, 领域, 提取, 抽取]
```

the above code combines the results generated by the co-occurance window of size 3~5;notice that the result is a little different from the original TextRank algorithm in 1.1

Also,you can set the number of keywords to extract by `TextRankWithMultiWin.setKeywordNumber(3);`


### 1.4 TextRank With TF-IDF

This part includes two algorithms: `TextRank-score multipy IDF` and `TextRank and TF-IDF vote together`, which are both based on the original TextRank and TF-IDF.

**To be able to run these two algorithms, remember to specify how to read the content of file under the directory in the `ReadFile.java `, the following part `3.2 ReadFile` will tell you details about this**

#### 1.4.1 TextRank-score multipy IDF

Source File: `TextRankWithTFIDF.java`

Based on the score generated by the original TextRank algorithm, this algorithm multipy the IDF value by the score for each word.In this way, it can consider the weight of a word in a corpus.**Therefore, this is an algorithm for a corpus(that is,it can't used to extract keywords for a single document)**
```java
String dir = "G:/corpusMini";
//TextRankWithTFIDF.setKeywordsNumber(3);//set the size of co-occurance window,default 5 
Map<String,List<String>> result= TextRankWithTFIDF.textRankMultiplyIDF(dir);
System.out.println(result);
```

Output is like this(suppose there are only 3 documents in the directory)
```
{
G:/corpusMini/00001.xml=[曹奔, 周某, 民警, 摩托车, 禁区], 
G:/corpusMini/00002.xml=[吴红娟, 翁刚, 婆婆, 丈夫, 分割], 
G:/corpusMini/00003.xml=[伤肾, 止痛片, 胡婆婆, 肾内科, 肾衰竭]
}
```
Also, you can change the number of keywords to extract as the commented line did above in the code.

#### 1.4.2 TextRank and TF-IDF vote together

Source File: `TextRankWithTFIDF.java`

This algorithm is based on both TextRank and TF-IDF.

In order to extract n keywords from a document, firstly extract 2*n candidate keywords with TextRank and TFIDF respectively, then select the words that co-occur in these candidate keywords as the final keywords, if the number of final keywords if not enough,select the left part from the result generated TFIDF.**Therefore, this is also an algorithm for a corpus.**

Sampel code is like this:
```java
String dir = "G:/corpusMini";
//TextRankWithTFIDF.setKeywordsNumber(3);//set the size of co-occurance window,default 5 
Map<String,List<String>> result= TextRankWithTFIDF.textRankTFIDFVote(dir);
System.out.println(result);
```

Output is like this:
```
G:/corpusMini/00007.xml=[曹奔, 周某, 民警, 摩托车, 困难],
G:/corpusMini/00002.xml=[翁刚, 吴红娟, 婆婆, 丈夫, 法院], 
G:/corpusMini/00018.xml=[止痛片, 伤肾, 肾衰竭, 胡婆婆, 肾内科]
```

Also, you can change the number of keywords to extract as the commented line did above in the code.


## 2. Evaluate

The Class `F1Score`  uses f1 score to evaluate the keywords extracted by the algorithm.You had got to take the keywords extracted by the algorithm and the keywords extracted manually as input.Sample code is like this:

```java
String title = "关键词抽取";
String content = "关键词自动提取是一种识别有意义且具有代表性片段或词汇的自动化技术。关键词自动提取在文本挖掘域被称为关键词抽取，在计算语言学领域通常着眼于术语自动识别，在信息检索领域，就是指自动标引。";
List<String> sysKeywords = TextRank.getKeyword(title, content);
String[] manualKeywords = {"关键词","自动提取"};
List<Float> result = F1Score.calculate(sysKeywords,manualKeywords);
System.out.println(result);
/*output*/
[20.0, 50.0, 28.57] // the three numbers represent precision = 20% recall=50% F1 =28.57%
```


## 3. Parse Documents

### 3.1 ReadDir
`ReadDir` Class provides a method to find all the paths of files under a certain directory,including the sub-directories.For example

```java
String dirPath = "G:/corpusMini";
List<String> fileList =  ReadDir.readDirFileNames(dirPath);
for(String file : fileList)
    System.out.println(file);
```

and the output is like this:
```
G:/corpusMini/00001.xml
G:/corpusMini/00002.xml
G:/corpusMini/test/00003.xml
```

as you can see,the method can also read the files of subdirectory `test`,because of this,remember not to take  `/`  as the last character of dirPath

### 3.2 ReadFile

`ReadFile` class is designed to load the content of file of a certain type, **remember you had got to implement the method `loadFile` in trems of the type of your file.** The default method in it is to parse the XML files [here](https://github.com/iamxiatian/data/tree/master/sohu-dataset) ,and the code is like this

```java
//remember to replace the following code to yours in terms of the type of your files
/*
String filePath = "G:/corpusMini/00001.xml";
ParseXML parser = new ParseXML();
String content = parser.parseXML(filePath, "content");
*/
```

