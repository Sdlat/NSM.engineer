# This is a heading
## Heading 2
### Heading 3
#### 4
##### 5
###### 6

---

__bold__  
**bold**  
**can just type b enter as well**  

---

*italic*  
_italic_  

---

***bold and italic***  
___bold and italic___  
**_bold and italic_**  
__*bold and italic*__

---

~~strike!~~

---

> This is a quote

---

* bullet list
-bullet list  
  - sub  
    - sub sub  
      * sub

---

1. one  
1. two
1. three?
2. four???

---

1. online - [elastic](https://elastic.co)
1. local links - [Readme](../NSM.engineer/README.md)

---

`systemctl start kibana`

```
suricata.yaml
```
---

| Column 1 | Column 2 | Column 3 | Column 4 |
| --- | --- | --- | --- |
| Data1 | Data 2 | Data 3 | Data 4|

---

<!-- Comment out-->

---
# Day 1 notes

---

## Lab topology
<img src="network.png">
<!-- image needs to be saved in the repo-->

  - edge Router  
    -Uplink  
    -Repo Server

---

## suricata Instal

1. install suricata  
`sudo yum install suricata`
1. edit config file  
```
...
vars:
#more specific
...
```
