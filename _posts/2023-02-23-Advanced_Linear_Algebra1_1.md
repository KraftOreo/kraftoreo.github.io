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
<br>
<div class=remarks>
    <b>Remarks</b>
    <p>
        In any vector space \(X\),
        <ol>
            <li>The zero vector \(\textbf{0}\) is unique</li>
            <p>
                <b>Proof:</b>
                Suppose the zero vector \(\textbf{0}\) is not unique, then we can assume there are two different zero vectors \(\textbf{0}_1, \textbf{0}_2\) in \(X\) (\(\textbf{0}_1\neq \textbf{0}_2\)). By the <mark class=red>identity</mark> property of a group, we have \(\textbf{0}_1 + \textbf{0}_2=\textbf{0}_1\) and \(\textbf{0}_1+ \textbf{0}_2=\textbf{0}_2\) which leads to a contradiction(\(\textbf{0}_1=\textbf{0}_2\))
            </p> 
            <li>\(0 \textbf{x}=\textbf{x}\) for all \(\textbf{x}\in X\)</li>
            <p>
                <b>Proof:</b>
                Suppose there's an \(\textbf{x}\) satisfy \(0 \textbf{x}\neq \textbf{0}\), thus we can assume that there is a vector \(\textbf{a}\neq 0\) satisfies \(0 \textbf{x}=\textbf{a}\). Thus we have for any scalar \(c\neq 0\): $$
                \begin{align*}
                    c \textbf{v} &= (c+0) \textbf{v}\\
                    &=c \textbf{v}+ 0 \textbf{v}\\
                    &=c \textbf{v}+\textbf{a} 
                \end{align*}
                $$

                If the equation \(c \textbf{v}=c \textbf{v}+ \textbf{a}\) holds, the vector \(\textbf{a}\) must be a zero vector other than \(0\), which leads to the contradiction that there can be only one zero vector in the vector space.
            </p>        
            <li>\(-1 \textbf{x}=-\textbf{x}\) for all \(\textbf{x}\in X\)</li>
            <b>Proof:</b>
            $$
            \begin{align*}
                -1 \textbf{x}+x&=-1 \textbf{x}+ 1 \textbf{x}\\
                &=(-1+1) \textbf{x}\\   
                &=0 \textbf{x}\\
                &=\textbf{0}
            \end{align*}
            $$
            which means \(-1 \textbf{x}\) is the inverse of \(\textbf{x}\). Thus we can conclude that \(-1 \textbf{x}=\textbf{x} \) 
        </ol>
    </p>
</div>
<br>
<div class=definition>
<b>Definition</b>
    <p>
        <b><i>Linear Map:</i></b>
        A <mark class=red><b>linear map</b></mark> between vector spaces \(X\) and \(Y\) over \(\mathbb{F}\) is a function \(\varphi: X \rightarrow Y\) satisfying:
        <ul>
            <li>\(\varphi(\textbf{v}+\textbf{w})=\varphi(\textbf{v})+\varphi(\textbf{w})\) <span>&nbsp;</span> for all \(\textbf{v,w}\in X\)</li>
            <li>\(\varphi(a \textbf{v})=a\varphi (\textbf{v})\)<span>&nbsp;</span> for all \(a \in \mathbb{F}\)</li>
        </ul>
        
    </p>
</div>