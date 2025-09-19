---
title: "Derivatives of Regular Expressions"
collection: cs_pl
type: "cs_pl"
permalink: /cs_pl/derivatives
date: 2024-03-23
published: true
---


<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-4MFKZNB73K"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'G-4MFKZNB73K');
</script>


I'll fix the remaining typos soon. I may upload an article on continuation in programming languages later.

Last Updated: March 23, 2024

Derivatives of Regular Expression
======
Regular expressions can be converted to deterministic finite automata (DFA) using well-known methods such as Thompson's construction. However, this involves intermediate steps through non-deterministic finite automata (NFA), which can be cumbersome. Additionally, backtracking-based regular expression engines may exhibit exponential time complexity in the worst case, exposing them to vulnerabilities such as ReDoS (regular expression denial of service) attacks. Recently, there has been a growing interest in derivative-based regular expression matching, which does not use backtracking (e.g., [RE#: High Performance Derivative-Based Regex Matching with Intersection, Complement, and Restricted Lookarounds](https://dl.acm.org/doi/10.1145/3704837)). \\
In this article, we explore the concept of the derivatives of regular expressions and how to construct equivalent automata using derivatives. For more detailed information, refer to Chapter 4.4 of *Elements of Automata Theory* by Jacques Sakarovitch. A basic understanding of regular expressions, finite automata, and group theory is assumed.

Definition
======
The derivative of a regular expression *e* with respect to a character *a* is defined as follows:

$∂_a$ &#8709; = &#8709;\\
$∂_a$  a = ε\\
$∂_a$  ε = &#8709;\\
$∂_a$  b = &#8709;\\
$∂_a$ (e1 e2) = ($∂_a$ e1) e2 + &nu;(e1)$∂_a$ e2 (← This is remarkably different from the typical differentiation of functions.)\\
$∂_a$ (e1 + e2) = $∂_a$ e1 + $∂_a$ e2\\
$∂_a e^* = (∂_a e) e^*$

Here, a,b ∈ Σ and a ≠ b

The auxiliary function for the given regular expression $\nu(e)$ is defined as follows:

$$
\nu(e) =
\begin{cases}
\varepsilon & \text{if } \varepsilon \in L(e) \\
\emptyset & \text{otherwise}
\end{cases}
$$

The derivative with respect to strings is extended as follows:\\
$$
\begin{aligned}
∂_a\ e &= e, \\
∂_{wa}\ e &= ∂_a(∂_w\ e)
\end{aligned}
$$

Example\\
For e = xyza(b + c)\* and w = "xyz", we have:
$∂_{wa}$ e = (b +c)\*

Language Described by $∂_w$ e
======

Regular expressions are closed under string derivatives (Theorem 4.1 in [Brzozowski, 1964](https://dl.acm.org/doi/10.1145/321239.321249)). Thus, we can define the language described by regular expression $∂_w$ e. The language denoted by $∂_w$ e corresponds to the left quotient of L(e) by the string "w".

$$
L(∂_w\ e) = w^{-1} L(e) = \{w' \mid ww' \in L(e)\}
$$

Constructing Automata Using Derivatives of Regular Expressions
======
  * Definition (Equivalence of Regular Expressions)
    * We write e1 ≡ e2 if L(e1) = L(e2). The relation ≡ divides expressions e into equivalence classes denoted by [e].
    * Example
      * For instance, L((0+1)\*) = L((0\*1\*)\*) = {0, 1}\*, hence (0+1)\* ≡ (0\*1*\)\*.
  * Here, the set $$Q = \{[\partial_w\,e_0] \mid w \in \Sigma^*\}$$ becomes finite. We can construct a DFA $A_{e_0}$ that accepts $L(e_0)$ by using these equivalence classes $[\partial_w\,e_0]$ as states. (This construction corresponds to reading character \( a \) from state \( e \) by differentiating \( e \) with \( a \).)
The DFA is defined as:
$$
A_{e_0} = (Q, \Sigma, \delta, [e_0], F)
$$
where
$$
\delta([e], a) = [\partial_w\ e_0], \quad
F = \{[e] \mid [e] \in Q, \varepsilon \in L(e)\}
$$

  * Weaker Equivalence Relation
    * Weaker Equivalence Relation  
  Using a weaker relation $\approx$ defined below instead of $\equiv$, we can ensure that the equivalence classes remain finite, allowing for the construction of a DFA.

The rules of $\,\approx\,$ are:

  $$
  e_1 + e_2 \approx e_2 + e_1
  $$

  $$
  (e_1 + e_2) + e_3 \approx e_1 + (e_2 + e_3)
  $$

  $$
  (e_1 e_2) e_3 \approx e_1 (e_2 e_3)
  $$

  $$
  (e^*)^* \approx e^*
  $$

  $$
  e + e \approx e
  $$

  $$
  e\,\emptyset \approx \emptyset\, e \approx \emptyset
  $$

  $$
  e\,\varepsilon \approx \varepsilon\, e \approx e
  $$

  $$
  \emptyset + e \approx e
  $$

  $$
  \varepsilon^* \approx \varepsilon
  $$

  $$
  \emptyset^* \approx \varepsilon
  $$



Example
======
To construct a DFA from the regular expression $e = (0 + 1)^* 00 (0 + 1)^*$:

Firstly, to determine the set of states for the DFA, compute all $[\partial_a e_0]$ using the equivalence $\approx$, where $$a = \{0, 1\} $$ and
$$
e_0 = \{ e, e_1, e_2, e_3 \}
$$
resulting in $2 \times 4$ patterns.

Define:
$$
\begin{align*}
e_1 &= e + 0(0 + 1)^* \\
e_2 &= e_1 + (0 + 1)^* \\
e_3 &= e + (0 + 1)^*
\end{align*}
$$

For example:
$$
\begin{align*}
\partial_0 e &\approx e + 0(0 + 1)^* = e_1 \\
\partial_1 e &\approx e \\
\partial_0 e_1 &\approx e + 0(0 + 1)^* + (0 + 1)^* = e_2 \\
\partial_1 e_1 &\approx e
\end{align*}
$$

Since $\varepsilon \in L(e_2)$ and $\varepsilon \in L(e_3)$, define:
$$
F = \{ e_2, e_3 \}
$$
Thus, with $$Q=\{e, e1, e2, e3\}$$, we can construct the DFA:
$$
A_e = (Q, \{0, 1\}, \delta, e, F)
$$
using the above transitions. (States [e2] and [e3] are collapsed into a single state due to their language equivalence under $\equiv$.


Here is the transition diagram of the DFA.
→ (To be added)

Appendix 1: The Definition of Regular Language using Finite Monoids and Homomorphisms
======
This definition will be particularly useful for defining regular tree languages using finite algebras.
  * (To be added)

Appendix 2: History of Regular Expressions
======
Key papers by McCulloch and Pitts, Kleene, and Thompson. Also see P. McCorduck’s *Machines Who Think* (W.H. Freeman and Company, 1979).
  * (To be added)


References
======
- Janusz A. Brzozowski. 1964. [Derivatives of Regular Expressions](https://dl.acm.org/doi/10.1145/321239.321249). J. ACM 11, 4 (Oct. 1964), 481-494.
- John E. Hopcroft, Rajeev Motwani, and Jeffrey D. Ullman. *Introduction to automata theory, languages, and computation*, 2nd edition, (March 2001).
- Sakarovitch J. *Elements of Automata Theory*. Thomas R, trans. Cambridge University Press; 2009.
- *Encyclopedia of Theoretical Computer Science*, 2022.






