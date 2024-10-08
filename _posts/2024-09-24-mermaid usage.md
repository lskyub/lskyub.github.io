---
layout: post
title: "Mermaid 플로우차트 작성 법"
date: 2024-09-24 09:00:00 +0900
categories: etc.
tags: [mermaid, notion, confluence]
---

개발중 flowchart를 그릴 필요가 있을 때 notion, confluence를 사용한다면 mermaid 를 사용해서 그리면 쉽게 그릴 수 있다.

## 사용방법

1. flowchart 방향 설정 TB, TD, BT, RL, LR 등 아래 이미지 와 같이 사용하여 방향을 설정 할수 있다.

![flowchart](/assets/img/0924_mermaid_1.png)<br>
2. mermaid 내부에서 사용 가능한 node 도형
```mermaid
graph TD
  id1[text가 들어간 박스]
  id2(모서리를 둥글게)
  id3((완전 원형))
  id4(((원 두개)))
  id5{마름모}
  id6[(data)]
```
![flowchart](/assets/img/0924_mermaid_2.png)<br>

3. mermaid에서 node 간 선 잇기
```mermaid
flowchart LR
    A --> B
    C -.-> D
    E --- F
    G -.- H
```
![flowchart](/assets/img/0924_mermaid_3.png)<br>

4. mermaid에서 node 간 선에 텍스트 넣기
```mermaid
flowchart LR
    A --text--> B
    C -.text.-> D
    E --text--- F
    G -.text.- H
```
![flowchart](/assets/img/0924_mermaid_4.png)<br>