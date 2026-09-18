# 環排九宮

3×3 / 4×4 的環狀滑動數字拼圖。整排左右或上下滑動，被推出邊界的方塊從另一端繞回來，把數字從左上排到右下。

## 檔案

```
loop-puzzle.html     遊戲本體，單一檔案，無外部相依
leaderboard.json     公開排行榜資料
```

兩個檔案要放在同一層。

## 部署到 GitHub Pages

1. 把兩個檔案 push 到 repo。
2. Settings → Pages → Source 選 `main` 分支的根目錄。
3. 想讓網址直接開遊戲，把 `loop-puzzle.html` 改名為 `index.html`。

本機測試要用 `http://`，不能直接雙擊開檔——`file://` 下瀏覽器會擋掉 `fetch`，公開榜讀不到。在資料夾裡跑：

```bash
python3 -m http.server 8000
```

然後開 http://localhost:8000。

## 排行榜怎麼運作

公開榜讀自 `leaderboard.json`，是唯讀的。玩家自己的成績存在瀏覽器的 localStorage，顯示時和公開榜合併，標「未送出」。同一個代號只留最快的一局。

更新公開榜的流程：

1. 玩家按「複製 JSON」，得到合併後的完整榜單。
2. 把內容貼進 `leaderboard.json`，commit。
3. 下次載入時那些成績就不再標「未送出」。

收別人的成績也一樣——請對方把 JSON 貼進 issue 或 PR，或用「匯入檔案」讀進來再匯出。

## 資料格式

```json
{
  "schema": "loop-puzzle.leaderboard",
  "version": 3,
  "boards": {
    "3x3": { "size": 3, "entries": [ { "name": "JQ", "ms": 18420, "seconds": 18.42, "moves": 27,
      "date": "2026-09-18", "time": "20:14:33", "timestamp": 1789503273921,
      "iso": "2026-09-18T12:14:33.921Z", "id": "m1q7x3k2" } ] },
    "4x4": { "size": 4, "entries": [] }
  }
}
```

排序先比 `ms`，同值再比 `moves`。`date` 和 `time` 是玩家當地時間，`timestamp` 是 Unix epoch 毫秒。匯出檔每個盤面保留 50 筆，畫面顯示前 10 名。

## 已知限制

成績沒有驗證機制，任何人都能改 JSON 或用 devtools 送出假成績。要擋就得把計時搬到伺服器。

榜單更新要手動 commit，不是即時的。如果之後想改成即時共用，把 `loadPublic()` 和 `saveMine()` 換成打 API 就好，資料格式不用動。
