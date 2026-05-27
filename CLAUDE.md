# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 概述

單一 HTML 檔案的靜態電費追蹤網頁應用。所有邏輯集中在 `index.html`，無需建構步驟、無需安裝套件、無需伺服器。

## 本地執行

直接用瀏覽器開啟 `index.html`，無任何開發伺服器或建構流程。

## 部署

- **GitHub 倉庫**：`https://github.com/42444977/NDHU_Electricity`
- **線上網址**：`https://42444977.github.io/NDHU_Electricity/`
- 更新方式：修改 `index.html` 後 `git add index.html && git commit && git push`，Pages 自動重新部署（約 1 分鐘）。

## 架構

所有內容集中於單一檔案（`index.html`）：HTML 結構、CSS、JavaScript。

**資料層** — `localStorage` 鍵值 `dorm_elec_v1`，儲存格式為 JSON 陣列 `[{ id, ts, bal }]`，其中 `ts` 為 Unix 毫秒時間戳，`bal` 為剩餘電費餘額（新台幣）。

**電費計費規則（每月1日重置）**
- 0–110 度 → $2.1/度（第一級距上限：$231）
- 111–700 度 → $2.68/度（累計第二級距上限：$1812.2）
- 701 度以上 → $3.97/度

**核心計算函式**
- `spentToKwh(spent)` — 將本月花費金額（NT$）反推為估計用電度數（費率級距的逆運算）
- `tierOf(kwh)` — 回傳目前級距編號、費率、標籤，以及距下一級距的剩餘度數
- `monthlySpent(readings, year, month)` — 加總指定月份內所有餘額「下降」量；餘額「上升」標記為充值，不計入用電

**已知限制**：若使用者在兩次記錄之間同時充值又消耗電費，只能觀察到餘額淨變化。偵測到餘額增加時會顯示充值警示橫幅，提示前後各記錄一次。

**圖表** — Chart.js 4.x 從 CDN（`cdn.jsdelivr.net`）載入，渲染至 `<canvas>` 元素。每次呼叫 `renderDashboard()` 時銷毀並重建。
