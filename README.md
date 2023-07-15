# taipei_play_list
這個程式的目的是為了找出google上每個地區的熱門景點，用selenium的方式將所有景點的基本資訊（地名，評分，評論連結）抓下來，再繼續用selenium將每個地點的評論連結連上後，抓出一定數量的評論數，再將這些評論數透過jieba斷字，最後製作成文字雲。如此一來即可將每個地點的概述一目了然，方便了解景點的情況。<br>
其中，在抓取評論時也會同時抓取評論時間，如此一來即可將評分與時間匯入tableau中繪出該景點的評分時間變化圖，用以了解整體評分的變化趨勢。<br>

## 主程式
主程式分成兩個部分，藉由.ipynb檔的特色來控制程式的執行。<br><br>

### 第一部分
* def search_place()<br>
selenium執行之後，自動搜尋"台北"。將台北的景點都顯示出來。<br><br>

* def get_all_place()<br>
抓出所有搜尋結果的: 景點名稱, google評分, google評論數, 地點簡述, 評論連結。<br>
因為景點的地址跟具體的評論不會直接顯示，所以要蒐集評論連結，之後再另外連上連結抓出地址跟評論。<br>
將每個景點的"景點名稱","google評分","google評論數","地點簡述","評論連結"，做成一個dic，再一一放進list中，最後再回傳這個list。<br><br>

* def group_place_list(place_list)<br>
將上一步驟get_all_place回傳的list代入，接著會將所有景點每10個進行分組，也就是說一個小組會有10筆景點資料，小組是list格式，最後再將每個小組裝進一個大list。然後回傳。<br>
這個步驟的目的是為了分開執行抓取更詳細資料時被google的"我不是機器人"阻擋selenium。<br><br>

* def get_what_group()<br>
控制現在要抓大組別裡面哪個小組資訊的func，會回傳現在要抓第幾組。<br><br>

* def get_place_address(all_group_place,get_group)<br>
用迴圈抓出所選取的小組內的景點資訊，透過裡面的評論連結連上，再抓出連結。最後再將新增了景點地址資料的新list回傳出來。<br><br>

* 將上面的function連續執行，最後再匯出excel place_data_0607.xlsx<br>

### 第二部分
這個階段將要抓取每個景點的評論。並將評論製作成文字雲。<br>

* def set_newest()<br>
將評論選至最新。因為對於景點來說，近期的評價更有參考價值，因此由最新的評論往後抓取。<br><br>

* def scroll()<br>
因為google評論是動態生成，因此需要控制selenium不斷向下滾動加載。這部分就是在設定讓selenium滾動頁面的方式，並設定滾動的上限。<br>
其中，設定上限是為了讓selenium不要抓取太多資料，避免耗費太多時間。<br><br>

* def get_all_comment()<br>
google評論的動態加載，是只要加載之後就會存留在原始碼內的資訊，因此我只要先將頁面滾動到設定上限，再直接讀取原始碼，就可以用beautifulsoup抓取出來。<br>
這個部分會將這個景點的"評論時間","評論內容","評分"用字典記錄起來，再放進list裡，最後回傳list。<br><br>

* def jieba_cut(comment_list)<br>
將上一個步驟中得到的該景點的評論資訊代入，這部分就會進行結巴斷字，並且統計分詞出現次數以及移除停用詞。最後再回傳處理完的結果。<br><br>

* def make_cloud(word_dic,place_name)<br>
這部分結巴斷字的結果製作成文字雲圖。使用的是WordCloud模組。製作完的圖片將另存到wordcloud_img資料夾內。<br>

* def get_place_comment(all_group_place,get_group)<br>
再次使用第一階段生成的大組別，以及get_all_place這個function，用以控制進行作業的組別。<br>
被選取的組別會經由迴圈，將組別內的景點一一抓取出來，然後用selenium連上評論連結，再使用scroll()，滾動生成一定資料後，使用get_all_comment()抓出原始碼再用beautiful擷取評論相關資料，再用jieba_cut斷句，接著用make_cloud生成文字雲。最後再將這個景點的所有評論、時間、評分回傳出來。<br>
其中，上面設計好的set_newest，scroll，get_all_comment，jieba_cut，make_cloud，都是在這個部分被使用，可以說這個部分才是真正在執行的部分。<br><br>

* 最後就是將剛剛的步驟回傳出來的資料按照景點名稱另存成一個excel的sheet，並將該景點的評分以及評分時間存入，最終會得到所有景點的評分以及時間。之後再將這個excel的時間進行資料處理，再匯入tableau中，即可繪出按照時間的評分變化趨勢圖。<br>

* 同時一開始得到的景點資訊excel也可以匯入tableau中進行操作。例如：對每個地區的景點進行檢視，看景點評分的分布。<br>

* 文字雲圖的結果在wordcloud_img資料夾內。
