# Ujian Akhir Semester

```
Nama            : Raka Adi Nugroho
NIM             : 14650015
Matakuliah      : Pemrosesan Bahasa Alami
```

## 1. Word Segmentation

Sample Data Inputan

```
Reza pergi ke (bagian kanan) rumah sakit Prof. Dr. Sardjito.
```

Hasil Word Segmentationnya

```
{
  "status": "success",
  "data": [
    "Reza",
    "pergi",
    "ke",
    "(",
    "bagian kanan",
    ")",
    "rumah sakit",
    "Prof.",
    "Dr.",
    "Sardjito",
    "."
  ]
}
```

Source-code

```java
def tokenizerComposite: Action[JsValue] = Action(parse.json) { implicit request: Request[JsValue] =>
    val string = (request.body \ "string").as[String]

    Try {
      sentenceTokenizer.tokenizeSentenceWithCompositeWords(string)
    } match {
      case Success(tokenized) =>
        val tokenList = new ListBuffer[JsValue]()

        for(token <- tokenized.asScala) {
          tokenList += Json.toJson(token)
        }

        Ok(Json.obj("status" -> "success", "data" -> tokenList))
      case Failure(failure) => InternalServerError(Json.obj("status" -> "error", "message" -> failure.toString))
    }
  }
```

## 2. Stemming

Inputan

```
sensored :v
```

Hasil Stemming

```
{
  "status": "success",
  "data": "sensored:v"
}
```

Source code

```
@Singleton
class StemmerController @Inject()(cc: ControllerComponents) extends AbstractController(cc) {
  val stem = new IndonesianStemmer

  def stemmer: Action[JsValue] = Action(parse.json) { implicit request: Request[JsValue] =>
    val string = (request.body \ "string").as[String]

    Try {
      stem.stemSentence(string)
    } match {
      case Success(stemmed) => Ok(Json.obj("status" -> "success", "data" -> stemmed))
      case Failure(failure) => InternalServerError(Json.obj("status" -> "error", "message" -> failure.toString))
    }
  }
}
```

## 3. Aturan Grammar Bahasa Indonesia

1. Aturan Kalimat \(Sentences\)
   ```
   <SENTENCE>::=<S><*CC> 0.005025125628140704
   <SENTENCE>::=<S><*.> 0.005025125628140704
   <SENTENCE>::=<VP><*.> 0.025399886757732325
   <SENTENCE>::=<SBARQ><*.> 0.006033689574633732
   <SENTENCE>::=<NP><*.> 0.009799398346218166
   <SENTENCE>::=<VP><*CC> 0.025399886757732325
   <SENTENCE>::=<SBARQ><*CC> 0.006033689574633732
   <SENTENCE>::=<NP><*CC> 0.009799398346218166
   <SENTENCE>::=<*NNP><*CC> 0.04811357459142889
   <SENTENCE>::=<*NN><*CC> 0.14294116079832545
   <SENTENCE>::=<*PRP><*CC> 0.066312808307904
   <SENTENCE>::=<*VBI><*CC> 0.0629511996602732
   <SENTENCE>::=<*NNG><*CC> 0.01937794240752085
   <SENTENCE>::=<*VBT><*CC> 0.0579260740321325
   <SENTENCE>::=<*FW><*CC> 0.013956224785901339
   <SENTENCE>::=<*CDC><*CC> 0.01718219402560373
   <SENTENCE>::=<*NNP><*.> 0.04811357459142889
   <SENTENCE>::=<*NN><*.> 0.14294116079832545
   <SENTENCE>::=<*PRP><*.> 0.066312808307904
   <SENTENCE>::=<*VBI><*.> 0.0629511996602732
   <SENTENCE>::=<*NNG><*.> 0.01937794240752085
   <SENTENCE>::=<*VBT><*.> 0.0579260740321325
   <SENTENCE>::=<*FW><*.> 0.013956224785901339
   <SENTENCE>::=<*CDC><*.> 0.01718219402560373
   <SENTENCE>::=<*JJ><*CC> 0.27228423484193026
   <SENTENCE>::=<ADJP><*CC> 0.013956224785901339
   <SENTENCE>::=<*JJ><*.> 0.27228423484193026
   <SENTENCE>::=<ADJP><*.> 0.013956224785901339
   ```
2. Frasa Kata Kerja \(VP\)
   ```
   <VP>::=<VP58><PP> 0.05157011109185776
   <VP>::=<*NEG><VP> 0.04020100502512563
   <VP>::=<*VBI><*RB> 0.17279948127735453
   <VP>::=<*VBI><PP> 0.06532663316582915
   <VP>::=<VP><NP> 0.06532663316582915
   <VP>::=<QP><*NN> 0.002512562814070352
   <VP>::=<*VBT><PP> 0.06030150753768844
   <VP>::=<VP59><NP> 0.002512562814070352
   <VP>::=<*VBT><NP> 0.12562814070351758
   <VP>::=<*IN><VP> 0.007537688442211056
   <VP>::=<*MD><VP> 0.04773869346733668
   <VP>::=<*VBT><ADVP> 0.05025125628140704
   <VP>::=<VP60><VP> 0.007537688442211056
   <VP>::=<VP61><VP> 0.007537688442211056
   <VP>::=<ADJP><VP> 0.008793969849246231
   <VP>::=<VP><PP> 0.03015075376884422
   <VP>::=<VP><VP> 0.007537688442211055
   <VP>::=<VP><ADVP> 0.011306532663316583
   <VP>::=<VP><*RP> 0.008793969849246231
   <VP>::=<*RB><VP> 0.1577241043929324
   <VP>::=<VP62><VP> 0.007537688442211056
   <VP>::=<*VBI><ADVP> 0.05527638190954774
   <VP>::=<*JJ><VP> 0.2697031073736027
   <VP>::=<VP63><ADVP> 0.007537688442211056
   <VP>::=<*VBT><SBAR> 0.002512562814070352
   <VP>::=<*VBI><ADJP> 0.005025125628140704
   <VP>::=<*VBI><NP> 0.0829145728643216
   <VP>::=<VP64><VP> 0.002512562814070352
   <VP>::=<ADVP><VP> 0.02512562814070352
   <VP>::=<VP65><PP> 0.012687960877182849
   <VP>::=<*VBT><*NN> 0.2036186680529082
   <VP>::=<VP66><NP> 0.011369106066732127
   <VP>::=<VP67><PP> 0.010050251256281407
   <VP>::=<*NEG><ADJP> 0.02135678391959799
   <VP>::=<NP><VP> 0.01074096536321454
   <VP>::=<VP68><VP> 0.01074096536321454
   <VP>::=<*VBI><*NN> 0.15587997458557154
   <VP>::=<VP69><ADJP> 0.010175398063112497
   <VP>::=<*SC><*VBI> 0.007537688442211055
   <VP>::=<*JJ><ADVP> 0.26844682596656755
   <VP>::=<*VBT><VP> 0.010050251256281407
   <VP>::=<ADVP><*VBI> 0.0628140703517588
   <VP>::=<*RB><*JJ> 0.2812691254164172
   <VP>::=<ADJP><PP> 0.016331658291457284
   <VP>::=<*MD><*JJ> 0.27786893651933137
   <VP>::=<*JJ><*VBI> 0.2916880319967183
   <VP>::=<*JJ><*NN> 0.3093012417235132
   <VP>::=<VP70><ADVP> 0.007537688442211056
   <VP>::=<*MD><PP> 0.002512562814070352
   <VP>::=<VP71><VP> 0.007537688442211056
   <VP>::=<VP72><PP> 0.2671905445595324
   <VP>::=<*MD><*VBT> 0.06909547738693467
   <VP>::=<*RB><*VBI> 0.17656832549846005
   <VP>::=<VP73><NP> 0.01074096536321454
   <VP>::=<*RB><NP> 0.00891911665607732
   <VP>::=<VP74><PP> 0.008793969849246231
   <VP>::=<*NN><*VBT> 0.11625657020735863
   <VP>::=<*MD><*VBI> 0.07412060301507538
   <VP>::=<VP75><PP> 0.007537688442211056
   <VP>::=<*NN><*VBI> 0.12128169583549933
   <VP>::=<*MD><*NN> 0.028894472361809045
   <VP>::=<*NN><VP> 0.1420608791081846
   <VP>::=<*JJ><PP> 0.2734719515947083
   <VP>::=<VP76><PP> 0.008793969849246231
   <VP>::=<*NN><ADJP> 0.07228672096112748
   <VP>::=<*JJ><NP> 0.28226592144395446
   <VP>::=<VP><*DT> 0.007537688442211056
   <VP>::=<*PRP><*VBI> 0.08296751959028861
   <VP>::=<*PRP><*VBT> 0.07794239396214792
   <VP>::=<*JJ><*NNP> 0.28559434517178905
   <VP>::=<*JJ><*PRP> 0.29014415360090784
   <VP>::=<*JJ><*NNG> 0.27841043712581204
   <VP>::=<*JJ><*VBT> 0.28917546918264797
   <VP>::=<*JJ><*FW> 0.2743395558997584
   <VP>::=<*JJ><*CDC> 0.2743395558997584
   <VP>::=<*RB><*NNP> 0.04723329290128805
   <VP>::=<*RB><*NN> 0.1539552601718269
   <VP>::=<*RB><*PRP> 0.06543252661776315
   <VP>::=<*RB><*NNG> 0.01849766071738
   <VP>::=<*RB><*VBT> 0.17405576268438971
   <VP>::=<*RB><*FW> 0.1520708380612741
   <VP>::=<*RB><*CDC> 0.15371810500825392
   ```
3. Frase Kata Benda \(NP\)
   ```
   <NP>::=<NP><*DT> 0.044061302681992334
   <NP>::=<*NN><*NN> 0.19412515964240104
   <NP>::=<*NN><NP> 0.1053639846743295
   <NP>::=<NP><QP> 0.009578544061302681
   <NP>::=<NP><NP> 0.019157088122605363
   <NP>::=<NP><ADJP> 0.01532567049808429
   <NP>::=<NP><PP> 0.012452107279693486
   <NP>::=<NP37><NP> 0.009578544061302681
   <NP>::=<ADVP><NP> 0.008620689655172414
   <NP>::=<NP38><ADJP> 0.013409961685823753
   <NP>::=<NP39><VP> 0.019157088122605363
   <NP>::=<*DT><NP> 0.011494252873563218
   <NP>::=<NP40><ADJP> 0.008620689655172414
   <NP>::=<*NNP><NP> 0.042145593869731796
   <NP>::=<NP41><*NN> 0.0810853076862929
   <NP>::=<*NN><ADJP> 0.14846743295019157
   <NP>::=<QP><NP> 0.011494252873563218
   <NP>::=<*NN><*CDO> 0.0038314176245210726
   <NP>::=<ADJP><NP> 0.008620689655172414
   <NP>::=<*NN><PP> 0.14559386973180077
   <NP>::=<NP42><NP43> 0.0019157088122605363
   <NP>::=<*VBT><NP> 0.009578544061302681
   <NP>::=<*FW><*FW> 0.003171990219295712
   <NP>::=<*NN><*NNP> 0.1309067688378033
   <NP>::=<QP><*NN> 0.1446360153256705
   <NP>::=<NP44><NP45> 0.0019157088122605363
   <NP>::=<*CDI><*NN> 0.1417624521072797
   <NP>::=<*CDI><NP> 0.008620689655172414
   <NP>::=<*WP><*RP> 0.0038314176245210726
   <NP>::=<*CC><*NN> 0.0019157088122605363
   <NP>::=<*PRP><*NN> 0.04468684025334271
   <NP>::=<*PRP><*CDC> 0.046123621862538114
   <NP>::=<*NNP><*NNP> 0.06768837803320561
   <NP>::=<NP46><ADJP> 0.009578544061302681
   <NP>::=<*DT><*NN> 0.1446360153256705
   ```
4. Frase Kata Sifat \(ADJP\)
   ```
   <ADJP>::=<VP><*JJ> 0.04081632653061224
   <ADJP>::=<*RB><*JJ> 0.10204081632653061
   <ADJP>::=<*NN><NP> 0.02040816326530612
   <ADJP>::=<*JJ><*CDP> 0.02040816326530612
   <ADJP>::=<*JJ><NP> 0.04081632653061224
   <ADJP>::=<*NEG><*JJ> 0.04081632653061224
   <ADJP>::=<*JJ><*JJ> 0.27613834478515026
   <ADJP>::=<*NN><*JJ> 0.025433288893446825
   <ADJP>::=<*SC><*JJ> 0.04081632653061224
   <ADJP>::=<*JJ><*NNP> 0.06638517475955899
   <ADJP>::=<*JJ><*NN> 0.16121276096645554
   <ADJP>::=<*JJ><*PRP> 0.08458440847603409
   <ADJP>::=<*JJ><*VBI> 0.022323872077566656
   <ADJP>::=<*JJ><*NNG> 0.03764954257565095
   <ADJP>::=<*JJ><*VBT> 0.02136601767143639
   <ADJP>::=<*JJ><*FW> 0.02136601767143639
   <ADJP>::=<*JJ><*CDC> 0.02136601767143639
   <ADJP>::=<*NN><*NNP> 0.05618109312690593
   <ADJP>::=<*NN><*NN> 0.1510086793338025
   <ADJP>::=<*NN><*PRP> 0.07438032684338103
   <ADJP>::=<*NN><*VBI> 0.012119790444913597
   <ADJP>::=<*NN><*NNG> 0.027445460942997886
   <ADJP>::=<*NN><*VBT> 0.011161936038783328
   <ADJP>::=<*NN><*FW> 0.011161936038783328
   <ADJP>::=<*NN><*CDC> 0.011161936038783328
   <ADJP>::=<*VBI><*JJ> 0.07065941954671316
   <ADJP>::=<*VBT><*JJ> 0.06563429391857245
   <ADJP>::=<ADJP><*JJ> 0.021664444672341295
   <ADJP>::=<*FW><*JJ> 0.021664444672341295
   <ADJP>::=<*CDC><*JJ> 0.021036303968823708
   ```
5. Kata Keterangan \(ADVP\)
   ```
   <ADVP>::=<*RB><*JJ> 0.27399877928152944
   <ADVP>::=<ADVP88><VP> 0.03225806451612903
   <ADVP>::=<*IN><*RB> 0.03225806451612903
   <ADVP>::=<*JJ><NP> 0.03225806451612903
   <ADVP>::=<*MD><*RB> 0.06451612903225806
   <ADVP>::=<*NEG><*RP> 0.03225806451612903
   <ADVP>::=<*JJ><*RP> 0.03225806451612903
   <ADVP>::=<*SC><*NN> 0.03225806451612903
   <ADVP>::=<*RB><*CDC> 0.03225806451612903
   <ADVP>::=<*JJ><*JJ> 0.03225806451612903
   <ADVP>::=<*VBT><*NN> 0.03225806451612903
   <ADVP>::=<ADVP89><ADJP> 0.03225806451612903
   <ADVP>::=<*RB><VP> 0.03225806451612903
   <ADVP>::=<*MD><*NN> 0.03225806451612903
   <ADVP>::=<ADVP462><ADJP> 0.06210604375231739
   <ADVP>::=<ADVP463><ADJP> 0.15693362995921395
   <ADVP>::=<ADVP464><ADJP> 0.08030527746879249
   <ADVP>::=<ADVP465><ADJP> 0.01804474107032505
   <ADVP>::=<ADVP466><ADJP> 0.03337041156840934
   <ADVP>::=<ADVP467><ADJP> 0.017086886664194785
   <ADVP>::=<ADVP468><ADJP> 0.017086886664194785
   <ADVP>::=<ADVP469><ADJP> 0.017086886664194785
   <ADVP>::=<*JJ><*NNP> 0.06210604375231739
   <ADVP>::=<*JJ><*NN> 0.15693362995921395
   <ADVP>::=<*JJ><*PRP> 0.08030527746879249
   <ADVP>::=<*JJ><*VBI> 0.01804474107032505
   <ADVP>::=<*JJ><*NNG> 0.03337041156840934
   ```



## 4. Syntactic Parsing

Inputan

```
sensored :v
```

Hasil Parsing

```
{
  "status": "success",
  "data": {
    "map": {
      "? ": "NP",
      "?": "NP",
      ".": ".",
      "?": "PP",
      "?": "VP"
    },
    "list": [
      "NP",
      "VP",
      "NP",
      "PP"
    ]
  }
}
```

Source Code

```
@Singleton
class PhraseController @Inject()(cc: ControllerComponents) extends AbstractController(cc) {
  val phraseChunker = new IndonesianPhraseChunker

  def chunker: Action[JsValue] = Action(parse.json) { implicit request: Request[JsValue] =>
    val string = (request.body \ "string").as[String]

    Try {
      phraseChunker.doPhraseChunker(string)
    } match {
      case Success(chunked) =>

        val chunkListString = new ListBuffer[String]()
        val chunkListJs = new ListBuffer[JsValue]()

        for(chunk <- chunked.asScala) {
          for(phrase <- chunk) {
            chunkListString += phrase
            if(chunk.indexOf(phrase) % 2 == 1) {
              chunkListJs += Json.toJson(phrase)
            }
          }
        }
        val chunkMap = chunkListString.grouped(2).collect { case ListBuffer(k, v) => k -> v }.toMap

        Ok(Json.obj("status" -> "success", "data" -> Json.obj("map" -> Json.toJson(chunkMap), "list" -> JsArray(chunkListJs))))
      case Failure(failure) => InternalServerError(Json.obj("status" -> "error", "message" -> failure.toString))
    }
  }
}
```



