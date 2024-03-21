---
title: LaTeX 사용을 위한 MathJax를 github page에 적용하기 
categories: [Posts,github]
tags: [github,page,chirpy,theme,mathjax,latex]
use_math: true
---

## Environment

- HW : MacBookPro 15,4(2019)
- OS : Sonoma 14.4
- github.io theme: chirpy

---

## Intro

1. mathjax script
2. 수식 입력: use_math

---


## 1. mathjax script

markdown post를 작성할 때 아래 script를 head에 포함시켜 로딩    

#### mathjax_surport.html 만들기
_includes/mathjax_surport.html  

```html
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    TeX: {
      equationNumbers: {
        autoNumber: 'AMS'
      }
    },
    tex2jax: {
      inlineMath: [['$', '$']],
      displayMath: [['$$', '$$']],
      processEscapes: true
    }
  });
  MathJax.Hub.Register.MessageHook('Math Processing Error', function (message) {
    alert('Math Processing Error: ' + message[1]);
  });
  MathJax.Hub.Register.MessageHook('TeX Jax - parse error', function (message) {
    alert('Math Processing Error: ' + message[1]);
  });
</script>

<script
  type="text/javascript"
  async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"
>
</script>

```

> 참고. chirpy starter에서는 _includes 폴더가 없어서 커스터마이징 안됨  


#### head에 mathjax_surport.html 포함시키기

_includes/head.html    

```html
{% raw %}
  {% if page.use_math %}
    {% include mathjax_support.html %}
  {% endif %}
{% endraw %}
```

## 2. 수식 테스트
Aquatic Carbonate System    

- The Solubility of Carbon Dioxide in Water   
$1.45g/L @ 25^\circ C, 1atm$   
$1.45g.CO_2 {1mol \over 44.01g} = 0.032947 mol.CO_2$

- Kw    
$H_2O \leftrightarrow H^+ + OH^-$

- Kh    
$CO_2(g) \leftrightarrow CO_2(aq)$    
$CO_2(aq) + H_2O(l)\leftrightarrow H_2CO_3(aq), pK_a = 3.8$   
$CO_2(g) + H_2O(l) \leftrightarrow H_2CO_3^*(aq), pK_a = 6.3$   

- K1, Carbon Dioxide Hydrolysis   
$H_2CO_3 \leftrightarrow HCO_3^- + H^+$   

- K2, Bicarbonate Dissociation    
$HCO_3^- \leftrightarrow CO_3^{2-} + H^+$   

- Total Carbonate   
$C_T = [H_2CO_3^*] + [HCO_3^-] + [CO_3^{2-}]$   

- Carbon Dioxide in water     
$[H_2CO_3^*] = K_h P_{CO_2}$    

- K1, K2, mass-bal, charge-bal    
  $[H_2CO_3^*] = 10^{-1.5} X 10^{-3.5} = 10^{-5} [M]$   

  ${[H^+][HCO_3^-] \over [H_2CO_3^*]} = K_1 = 10^{-6.3}$    

  ${[H^+][CO_3^{2-}] \over [HCO_3^-]} = K_2 = 10^{-10.3}$   

  $[H^+][OH^-] = K_w$   

  mass balance    
  $C_T = [H_2CO_3^*] + [HCO_3^-] + [CO_3^{2-}]$   

  charge balance    
  $[H^+] - [HCO_3^-] - 2[CO_3^{2-}] - [OH^-] = 0$   
