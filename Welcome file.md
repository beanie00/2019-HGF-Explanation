---


---

<h1 id="hgf-hierarchical-gaussian-filter">HGF (Hierarchical Gaussian Filter)</h1>
<h1 id="왜-hgf를-사용하는가">1. 왜 HGF를 사용하는가</h1>
<p>학습은 주변 환경에 대한 agent의 믿음이 이미 알고 있던 정보와 새로운 정보를 통합함으로써 향상되는 과정으로 이해할 수 있다.</p>
<p>이러한 학습을 이해하는데 현재 크게 두가지의 메커니즘(<em><strong>Reinforcement Learning, Bayesian Learning</strong></em>)이 사용된다. 그러나 이 두 메커니즘은 각각 여러가지 단점을 가진다. 여기서 설명할 HGF는 그러한 단점을 보완하기 위하여 설계되었다. 먼저 각각의 알고리즘이 무엇인지, 그리고 단점은 어떤 것이 있는지 살펴보자.</p>
<h2 id="bayesian-learning">Bayesian Learning</h2>
<p>P(A|B)=\frac { P(B|A)P(A) }{ P(B) }</p>
<p>이 식은 베이즈 정리식이다. 이 식에서 B는 관측된 data, A는 불확실성을 계산하고자 하는 대상으로 볼 수 있다.</p>
<p><code>* P(A)</code>는 불확실성을 계산하고자 하는 대상의 <strong>사전 확률</strong> (과거의 경험으로 알 수 있다)<br>
<code>* P(B)</code>는 data의 사전확률 (정규화 상수 역할을 한다; 현재의 증거라는 의미에서 <strong>evidence</strong>라고도 한다)<br>
<code>* P(B|A)</code>는 A에 따라 data가 관측될 조건부 확률 (A에 대한 과거의 경험에 기초한 data가 관측될 확률; <strong>likelihood</strong>라고도 한다.)<br>
<code>* P(A|B)</code>는 이러한 data(B)가 관측되었을 때 A의 확률 (구하고자 하는 <strong>사후확률</strong>)</p>
<p>따라서 Bayesian learner은 A의 사전확률 P(A)를 알고 있을 때 data(B)를 관측함으로써 A의 확률(<span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mi>P</mi><mo>(</mo><mi>A</mi><mi mathvariant="normal">∣</mi><mi>B</mi><mo>)</mo></mrow><annotation encoding="application/x-tex">P(A|B)</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord mathit" style="margin-right: 0.13889em;">P</span><span class="mopen">(</span><span class="mord mathit">A</span><span class="mord">∣</span><span class="mord mathit" style="margin-right: 0.05017em;">B</span><span class="mclose">)</span></span></span></span></span>)을 업데이트 시켜나간다.<br>
즉, <strong>A의 사후확률</strong>을 구한다. </p>
<p>그러나 이 이론은 몇가지 단점을 가지고 있다.</p>
<ol>
<li><span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mi>P</mi><mo>(</mo><mi>A</mi><mi mathvariant="normal">∣</mi><mi>B</mi><mo>)</mo></mrow><annotation encoding="application/x-tex">P(A|B)</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord mathit" style="margin-right: 0.13889em;">P</span><span class="mopen">(</span><span class="mord mathit">A</span><span class="mord">∣</span><span class="mord mathit" style="margin-right: 0.05017em;">B</span><span class="mclose">)</span></span></span></span></span>를 계산하기 위해서는 위의 수식에서 <span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mi>P</mi><mo>(</mo><mi>B</mi><mo>)</mo></mrow><annotation encoding="application/x-tex">P(B)</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord mathit" style="margin-right: 0.13889em;">P</span><span class="mopen">(</span><span class="mord mathit" style="margin-right: 0.05017em;">B</span><span class="mclose">)</span></span></span></span></span>를 계산해야 한다. 이 값은 <span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mi>P</mi><mo>(</mo><mi>B</mi><mo>)</mo><mo>=</mo><msub><mo>∫</mo><mi>A</mi></msub><mrow><mi>P</mi><mo>(</mo><mi>B</mi><mi mathvariant="normal">∣</mi><mi>A</mi><mo>)</mo></mrow></mrow><annotation encoding="application/x-tex">P(B)=\int_{A} {P(B|A)}</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord mathit" style="margin-right: 0.13889em;">P</span><span class="mopen">(</span><span class="mord mathit" style="margin-right: 0.05017em;">B</span><span class="mclose">)</span><span class="mspace" style="margin-right: 0.277778em;"></span><span class="mrel">=</span><span class="mspace" style="margin-right: 0.277778em;"></span></span><span class="base"><span class="strut" style="height: 1.16082em; vertical-align: -0.35582em;"></span><span class="mop"><span class="mop op-symbol small-op" style="margin-right: 0.19445em; position: relative; top: -0.00056em;">∫</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.122511em;"><span class="" style="top: -2.34418em; margin-left: -0.19445em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathit mtight">A</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.35582em;"><span class=""></span></span></span></span></span></span><span class="mspace" style="margin-right: 0.166667em;"></span><span class="mord"><span class="mord mathit" style="margin-right: 0.13889em;">P</span><span class="mopen">(</span><span class="mord mathit" style="margin-right: 0.05017em;">B</span><span class="mord">∣</span><span class="mord mathit">A</span><span class="mclose">)</span></span></span></span></span></span>를 계산함으로써 얻을 수 있다. 그러나 매우 간단한 경우를 제외하고는 이 적분을 계산하기는 매우 복잡하다. 따라서 이 적분을 실제로 agent가 정보 하나가 들어올 때 마다 수행한다고 보기 어렵다.<em>(현실적이지 않다.)</em></li>
<li>Bayesian leaning은 정보가 어떻게 다루어져야 하는지를 규정하는 normative framework를 구성한다. 그러나 실제 상황에서는 만약 모든 agent가 같은 prior knowledge를 가지고 있더라도 새로운 정보를 똑같이 받아들이지 않는다. 즉, Bayesian Learning은 inter-individual variability를 무시한다.<em>(베이즈 정리 식에서 individual 정보를 주는 부분은 없다.)</em></li>
</ol>
<h2 id="reinforcement-learning-rl">Reinforcement Learning (RL)</h2>
<p>[[연합강도 그림]] <br>
고전적 조건형성에서 CS와 UCS의 연합강도를 V라고 둘 수 있다. 이 연합강도 V는 시행이 반복될 수록 강해지지만 무작정 강해지지는 않고 그림에서 볼 수 있듯이 로그 함수꼴로 증가한다. 이 때 시행을 한 번 할 때마다 연합강도의 변화량을 \Delta V라고 하면 \Delta V_n \sim  V_{max}-V_n, 즉 \Delta V는 V_{max}-V_n에 비례한다. <br>
이 식은 최대 연합강도와 현재의 연합강도의 차이가 클 때는 학습이 빨리 일어나지만 현재의 연합강도가 최대 연합강도와 비슷할 때는 학습이 잘 되지 않음을 의미한다. 이 때, 상수 C를 포함시키면 이 식은 \Delta V_n=c(V_{max}-V_n)로 나타낼 수 있는데, c는 learning rate <em>(individual 마다 다른 값을 가진다)</em> 이다. <br>
이 이론은 다음과 같은 특징을 지닌다.</p>
<ul>
<li>Bayesian learning이 normative framework를 가지고 있는 반면, Reinforcement Learning은 descriptive 접근법을 사용한다. 즉, 어떠한 확률 이론을 가정하지 않고 그저 agent가 다른 자극과 행동으로부터 "value"를 학습하는 과정을 기술한다. 따라서 조현병과 우을증과 같은 일탈적 학습도 모델링 할 수 있다.</li>
<li>개념적으로 간단하고 계산이 간편하다.</li>
</ul>
<p> 그러나 이 이론에도 중요한 단점이 있다.</p>
<ul>
<li>확률 이론을 따르지 않는 heuristic한 접근법이기 때문에 환경적 상태와 행동의 결과가 agent에게 알려지지 않은 채로 학습해야 하는 실제 상황에 잘 맞지 않는다.</li>
</ul>
<p><code>따라서 업데이트된 Bayesian model(HGF)을 통하여 Bayesian 모델과 RL 모델의 단점을 극복하고자 하였다. 또한 이렇게 업데이트된 Bayesian 식은 RL 모델과 유사한 형태를 띄게 된다.</code></p>
<h2 id="hgf">HGF</h2>
<p>업데이트된 Bayesian model(HGF)은 다음과 같은 특징을 지닌다.</p>
<ul>
<li>다른 Bayesian learning scheme처럼 sensory signal이 어떻게 발생하는지에 대한 확률적인 가정을 한다.</li>
<li>Gaussian random walk로 업데이트되는 위계적 상태를 구성한다. 이 때, 각 walk의 step size는 위계의 다음 highest level로 부터 결정된다.</li>
<li>각 위계의 바로 전 level과 바로 다음 level을 연결하는 parameter를 통해 <strong>individual difference를 나타낸다.</strong></li>
<li>이 모델은 <em>mean-field approximation</em> 과 <em>Laplace approximation</em> 등을 사용하여 parameter을 추정한다. <strong>(기존의 Bayesian model이 복잡한 적분식을 포함한 것과 달리 간단히 계산할 수 있다)</strong> 이 때 계산된 parameter값은 RL 모델과 유사한 형태로 나타난다.</li>
</ul>
