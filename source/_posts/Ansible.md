---
title: Ansible 學習筆記 01
date: 2023-02-20 10:48:15
tags: ansible,note
---

## Why Ansible
### 在一個 AWS instance 上架設伺服器：
1. 安裝環境 (Python、Docker...)
2. 設定防火牆、安裝/匯入認證
3. 安裝 AWS Cli tool
4. 設定 AWS Cli tool
5. Pull docker images
....
....
....

### 在四個 AWS instance 上架設伺服器：
重複以上步驟 x4

### 在一百個 AWS instance 上架設伺服器：
重複以上步驟 x100

<iframe width="420" height="315"
src="https://www.youtube.com/embed/IMUT8EhTqJM">
</iframe>

:::success
:point_right: 需要一個工具可以重複執行相同步驟
:::
###
``` mermaid
graph LR;
安裝環境-->防火牆設定A;
安裝環境-->防火牆設定B;
防火牆設定A-->認證機制1;
防火牆設定A-->認證機制2;
防火牆設定A-->認證機制3;
防火牆設定B-->認證機制3;
防火牆設定B-->認證機制4;
防火牆設定B-->認證機制5;
認證機制1-->.;
認證機制1-->..;
認證機制1-->...;
認證機制3-->..;
認證機制3-->....;
認證機制3-->.....;
認證機制4-->...;
認證機制4-->....;
認證機制4-->......;
認證機制5-->.....;
認證機制5-->......;
```