# 單字卡整合管理系統 (WordCards System)

## 核心功能特色
 
### 1. 資料檔讀取與解析 (TSV File Reader)
* **Unicode 文字格式支援**：支援讀取以 Unicode (UTF-8) 文字格式儲存的 `.txt` 或 `.tsv` 檔案，確保各國語言音標與特殊符號能完美正常顯示。
* **高效字串解析**：利用 `Tab (\t)` 符號即時切換與分割欄位，並透過 LINQ 語法將多行解釋自動串接整合。

### 2. 物件導向架構 (自訂類別集合)
* **`WordItem` 類別**：封裝單字、音標、音檔路徑與解釋。覆寫 `ToString()` 以利 UI 控制項直接載入，並實作 `ToLineString()` 將物件還原為符合標準 TSV 格式的字串。
* **`WordCollection` 類別**：繼承自 `Collection<WordItem>` 的自訂集合，內建 `LoadFromStringArray()` 與 `SaveToFile()`，實現資料載入與即時檔案回存（Data Persistence）的自動化管理。

### 3. 雙表單互動與即時編輯 (Form CRUD)
* 支援滑鼠雙擊單字清單，即可彈出編輯視窗（`frmEditWord`）。修改內容後按下儲存，變更將直接寫回底層 `_WordList` 集合，並即時自動更新文字檔。

### 4. 多媒體聲音發音播放
* 整合 **Windows Media Player COM 組件 (WMPLib)**，系統會自動偵測本機音效檔案是否存在，並進行非同步的真人單字發音播放。

### 5. 強化學習優化功能（進階亮點 🌟）
* **隨機亂序播放 (Shuffle Mode)**：打破傳統死記單字順序的弊端。啟用後，系統將透過全域 `Random` 機制隨機跳選清單，大幅提升背單字成效。
* **播放速度滑桿 (Playback Speed Slider)**：介面導入 `TrackBar` 控制項，允許使用者自由調整自動播放的間隔時間（1秒 至 5秒），即時變更 `Timer.Interval` 以符合個人學習節奏。

---

## 系統介面展示

系統主要由三個主要區塊與雙表單構成：
* **`frmTSVFile` (TSV資料檔讀取視窗)**：利用 `ListView`（Details 檢視）呈現表格化的單字清單，提供全功能選單。
* **`frmWordCards` (單字卡主視窗)**：大字體呈現單字與音標，具備 `ListBox` 快速選單與自動播放控制區。
* **`frmEditWord` (編輯單字視窗)**：群組化（`GroupBox`）欄位設計，提供清晰的資料修改介面。

<img width="784" height="443" alt="image" src="https://github.com/user-attachments/assets/9ed3a9fb-9c75-4aaa-b7e2-f4c71a86b5e9" />

<img width="384" height="650" alt="image" src="https://github.com/user-attachments/assets/3605a1e7-41d3-49c5-9908-c1650b2bed72" />


---

## 專案核心架構與類別

### 單字資料模型 (`WordItem.cs`)
```csharp
public class WordItem {
    public string Word { get; set; }
    public string Phonogram { get; set; }
    public string SoundPath { get; set; }
    public string Explain { get; set; }

    public WordItem(string str) {
        string[] strLists = str.Split('\t');
        if (strLists.Length >= 3) {
            Word = strLists[0];
            Phonogram = strLists[1];
            SoundPath = strLists[2];
            Explain = string.Join(Environment.NewLine, strLists.Skip(3));
        }
    }

    public override string ToString() => Word;

    public string ToLineString() {
        string strExplain = Explain.Replace(Environment.NewLine, "\t");
        return $"{Word}\t{Phonogram}\t{SoundPath}\t{strExplain}";
    }
}
