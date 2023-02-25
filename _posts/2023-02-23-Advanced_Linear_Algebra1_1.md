---
title:  "Vector Spaces and Linearity"
mathjax: true
layout: post
categories: media
---

# Algebraic structures
To start, we need some basic information of algebraic structure:
<div class=definition>
<b>Definition</b>
<p>
    <b><i>Group:</i></b>
    A <b><mark class=red>group</mark></b> is a set \(\textit{G}\)  and associative binary opration * with:

    <ul>
        <li><b>closure:</b> \(\textit{a,b} \in \textit{G}\) implies \(\textit{a} * \textit{b} \in \textit{G}\)</li>
        <li><b>identity:</b> there exists \(\textit{e} \in \textit{G}\) such that \(\textit{a}*\textit{e} = \textit{e}*\textit{a} =\textit{a}\) for all \( \textit{a}\in \textit{G}\);</li>
        <li><b>inverses:</b> for all \(\textit{a} \in \textit{G}\), there is \(\textit{b} \in \textit{G}\) such that \(\textit{a}*\textit{b}=\textit{e}\)</li>
    </ul>
A group is <mark class=red><b>abelian</b></mark> if \(\textit{a}*\textit{b}=\textit{b}*\textit{a}\) for all \(\textit{a},\textit{b} \in \textit{G}\)
</p>
</div> 
<br>
<div class=definition>
    <b>Definition</b>
    <p>
        <b><i>Field:</i></b>
        A <mark class=red><b>field</b></mark> is a set \(\mathbb{F}\) or \(\textit{K}\) containing \(1 \neq 0\) with two binary operations: \(+\) and \(\dot\)(multiplication) such that:
        <ol>
            <li>\(\mathbb{F}\) is an abelian group under addition;</li>
            <li>\(\mathbb{F}\setminus \{0\}\) is an abelian group under multiplication;</li>
        </ol>
    </p>
</div>
<br>
<div class=remarks>
    <b>Remarks</b>
    <p>
        <ol>
            <li>\(\mathbb{Q},\mathbb{R},\mathbb{C},\mathbb{Z}_p\)(prime \(p\)), \(\mathbb{Q}(\sqrt{2}):=\{a+b \sqrt{2}:a,b \in \mathbb{Q}\}\) are all fields</li>
            <li>\(\mathbb{Z}\) is not a field. Nor is \(\mathbb{Z}_n\)(composite n)</li>
            <li>The <i>additive identity</i> is 0, and the inverse of \(a\) is \(-a\)</li>
            <li>the <i>multiplicative identity</i> is 1, and the inverse of \(a\) is \(a^{-1}\), or \(\frac{1}{a}\)</li>
        </ol>
    </p>
</div>
# Vector Spaces
Now we can define vector spaces:
<div class=definition>
<b>Definition</b>
    <p>
        <b><i>Vector Space:</i></b>
        A <mark class=red><b>vector space</b></mark> is a set \(X\)("vectors") over a filed \(\mathbb{F}\)("scalars") such that:
        <ol>
            <li>\(X\) is an <mark class=blue>abelian group</mark> under addition;</li>
            <li>\(X\) is closed under <mark class=blue>scalar multiplication</mark>;</li>;
            <li>+ and * are "compatible" via natural <mark class=blue>associative</mark> and <mark class=blue>distributive</mark> laws relating the two:</li>
            $$
            \begin{align*}
                & a(b \mathbf{v})=(ab)\mathbf{v}, &&\text{for all }a,b\in \mathbb{F}, \mathbf{v} \in X;\\
                & a(\mathbf{v}+\mathbf{w})=a\mathbf{v}+a\mathbf{w}, & &\text{for all }a\in \mathbb{F}, \mathbf{v},\mathbf{w} \in X;\\
                &(a+b)\mathbf{v}=a\mathbf{v}+b\mathbf{v},& &\text{for all }a,b\in \mathbb{F}, \textbf{v},\textbf{w}\in X;\\
                &1 \textbf{v}=\textbf{v},& &\text{for all } \textbf{v}\in X
            \end{align*} 
            $$ 
        </ol>
        
    </p>
</div>
<br>
<div class=random>
<b>Intuition</b>
    <p>
        Think of a vector space as a <mark class=blue>set of vectors</mark> that is:
        <ol>
            <li>
                Closed under addition, subtraction, and scalar multiplication;
            </li>
            <li>Equipped with the "natural" associative and distributive laws</li>
        </ol>
        
    </p>
</div>