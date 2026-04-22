---
name: ideal-customer-profile-dattica
description: Define 2 ICPs principales (CTO tech-focused, CFO business-focused) con buyer journey, pain points específicos, objections handling y personalization triggers para página web. Use when this capability is needed.
metadata:
  author: chris78rey
---

# Skill: Ideal Customer Profile (ICP) — Buyer Journey & Personalization

## Propósito Estratégico

Cada visitante a da-tica.com es potencialmente CTO o CFO. La página debe "branching" automáticamente su experiencia según perfil detectado (o explícitamente seleccionado). Este skill define dos ICPs y sus journeys.

---

## ICP #1: CTO (Technical Decision-Maker)

### Perfil Demográfico

**Título:** Chief Technology Officer / VP Engineering / Head of Architecture

**Industria:** Fintech, banca, gobierno, healthcare, retail de alto volumen

**Empresa:** Midmarket (200-2000 empleados) a Enterprise (2000+)

**Región:** Latinoamérica (Ecuador, Colombia, Chile, Argentina)

**Edad:** 35-55 años

**Experience:** 15+ años en tech, 5+ años en rol de liderazgo

### Psicografía (Motivaciones Profundas)

**Motivaciones Intrínsecas:**
- Construir sistemas que escalen sin comprometer seguridad
- Atraer talento técnico (reputación en comunidad)
- Eliminar deuda técnica que bloquea innovación
- Mantener control técnico sin "caer" a cloud puro

**Miedos/Objections:**
- "¿Rust es un riesgo? ¿Puedo hallar talento?" (skill gap)
- "¿Y si la modernización late? ¿Downtime?" (execution risk)
- "¿Estoy pagando más por menos valor?" (ROI no claro)
- "¿Otra consultora que no entiende mi contexto?" (vendor lock-in fear)

**Valores:**
- Transparencia técnica (odian "black box" consulting)
- Propiedad de datos (sovereignty, no SaaS)
- Métricas reales (benchmarks, no marketing)
- Agility sin sacrificar stability

### Pain Points Específicos (Rank by Acuteness)

1. **Latencia en sistemas críticos** (P99 >100ms)
   - Síntoma: "Nuestros usuarios reportan lentitud en picos"
   - Causa raíz: Java + GC pauses + ineficiencia en queries
   - Impact: Churn de usuarios, pérdida de confianza

2. **Deuda técnica en Java legacy** (monolito 15+ años)
   - Síntoma: "Cada feature toma 3x más tiempo que debería"
   - Causa raíz: Acoplamiento, falta de tests, documentación desactualizada
   - Impact: Talento junior huye, senior burnout

3. **Costo de infraestructura fuera de control** (OPEX +40% YoY)
   - Síntoma: "Factura AWS creciendo sin crecimiento de tráfico"
   - Causa raíz: Pods Java ineficientes, overprovisioning
   - Impact: Presupuesto IT capturado por infra, no por innovación

4. **Silos de datos impidiendo IA** (datos en N bases diferentes)
   - Síntoma: "Queremos usar IA pero nuestros datos están fragmentados"
   - Causa raíz: Data warehouse aislado de transaccional, silos vectoriales
   - Impact: IA inconsistente, hallucinations, riesgo regulatorio

5. **Auditoría/cumplimiento regulatorio manual** (6+ meses)
   - Síntoma: "Reguladores preguntan explicabilidad, no tenemos proceso"
   - Causa raíz: Logging inconsistente, no hay trazabilidad de datos
   - Impact: Riesgo de multas, sorpresas en inspecciones

### Buyer Journey (CTO)

#### Stage 1: Awareness (Problema está aquí, solución no clara)

**Triggers:**
- Lee artículo blog "Rust vs Java en Banca" (SEO organic)
- Colega recomienda da-tica (word of mouth)
- Busca "Oracle 26ai RAC optimization" (Google)
- LinkedIn post sobre SPDP compliance (paid)

**CTOs son escépticos en Awareness.** Necesitan:
- Proof punto 1: Casos reales (anonimizados)
- Proof punto 2: Benchmarks públicos (no marketing)
- Proof punto 3: Autoridad técnica (sobre dónde se ubicó CTO)

**Website Experience (Awareness Stage):**
```html
<!-- Hero: Technical + Honest -->
<h1>Latencia de 325K transacciones en 40 segundos. 
  Así modernizamos sistemas de misión crítica.</h1>

<!-- Pain Point Section: Technical Depth -->
<section id="cto-pain-points">
  <h2>Los 3 Retos que tu CTO Enfrenta en 2026</h2>
  <!-- GC pauses gráfico, monolito acoplamiento, OPEX chart -->
</section>

<!-- Authority: Benchmarks, no marketing -->
<section id="metrics">
  <h3>20+ años en defensa, banca, salud pública</h3>
  <!-- Real casos (NDA), código open source, papers técnicos -->
</section>

<!-- CTA: Technical Depth, no sales pitch -->
<button>Ver arquitectura técnica detallada</button>
```

#### Stage 2: Consideration (Solución identificada, viabilidad incierta)

**Triggers:**
- Ha leído 2-3 artículos técnicos
- Descargó benchmarks o checklist
- Visita sección de "servicios" múltiples veces
- Vuelve al sitio después de 3+ días (intent signal)

**CTOs necesitan:**
- Validación: "¿Otros lo hicieron?" (casos)
- Claridad: "¿Cómo se vería mi arquitectura?" (diagrama)
- Risk mitigation: "¿Cuál es el plan si algo falla?" (runbook)

**Website Experience (Consideration Stage):**
```html
<!-- Case Studies (Anonimizados) -->
<section id="case-studies">
  <article>
    <h3>Cliente Fintech Ecuador: De 50 pods Java a 12 pods Rust</h3>
    <p>40% reducción OPEX en 18 meses. P99 latencia: 200ms → 45ms.</p>
    <!-- Timeline, métricas antes/después, equipo involucrado -->
  </article>
</section>

<!-- Architecture Diagrams Interactive -->
<section id="architecture">
  <h3>Tu Arquitectura Convergente (Oracle 26ai + Rust)</h3>
  <!-- Diagrama SVG, tabs para cada componente -->
</section>

<!-- FAQ Técnico (Objections Handling) -->
<section id="faq-cto">
  <details>
    <summary>¿Rust realmente reduce latencia? ¿En qué servicios?</summary>
    <p>Sí. Rust reduce latencia en servicios I/O bound + críticos...</p>
  </details>
  <details>
    <summary>¿Cuál es el riesgo de migración? ¿Y si needed to rollback?</summary>
    <p>Mitigamos con canary deployment, blue-green, full testing...</p>
  </details>
</section>

<!-- CTA: Schedule technical deep-dive -->
<button>Agendar sesión técnica (30 min, sin compromiso)</button>
```

#### Stage 3: Decision (Viabilidad validada, presupuesto = barrier)

**Triggers:**
- Ha descargado 3+ recursos
- Pasó 10+ minutos en sección de servicios
- Hizo click en CTA "agendar sesión"
- Visitó sección de pricing

**CTOs necesitan (ahora sí):**
- Presupuesto claro (fixed price option)
- Timeline realista (roadmap con hitos)
- Soporte post-venta (SLA, no abandono)

**Website Experience (Decision Stage):**
```html
<!-- Pricing: Transparent, Hito-based -->
<section id="pricing">
  <article>
    <h3>Modernización Legacy & Oracle Convergente</h3>
    <p class="price">$250K - $500K USD</p>
    <p class="timeline">6-12 meses</p>
    <ul>
      <li>✓ Arquitectura convergente (Mes 2)</li>
      <li>✓ MVP en producción (Mes 6)</li>
      <li>✓ Full deployment + training (Mes 12)</li>
    </ul>
  </article>
</section>

<!-- SLA & Support -->
<section id="sla">
  <h3>Garantías & Soporte</h3>
  <ul>
    <li>SLA: 99.9% uptime en infraestructura desplegada</li>
    <li>Support: 12 meses incluido, luego modelo retainer</li>
    <li>Runbooks: Documentación operacional completa</li>
    <li>Training: Team da-tica + tu equipo durante proyecto</li>
  </ul>
</section>

<!-- CTA: Start Engagement -->
<button>Solicitar propuesta formal (RFP)</button>
```

### Objection Handling (CTO-Specific)

| Objection | Root Cause | Counter |
|-----------|-----------|---------|
| "¿Por qué Rust y no [Kotlin/Go/Zig]?" | FUD, opciones abrumadoras | Benchmarks lado-a-lado, trade-offs honestos |
| "¿Y si no encontramos talento Rust?" | Talent market concern | Ofrecemos training, partnerships con bootcamps |
| "¿Downtime durante migración?" | Execution fear | Blue-green + canary, zero-downtime strategy |
| "¿Estamos locked-in con ustedes?" | Vendor lock-in fear | Código abierto, runbooks en tu repo, full knowledge transfer |
| "¿ROI real o marketing?" | Skepticism legítimo | Benchmarks públicos, contratos con upside sharing |

### Personalization Triggers (Website)

Detectar CTO automáticamente:
```javascript
// Persona Detection
const personaSignals = {
  isCTO: [
    hasVisited('/blog/rust-vs-java'),
    hasDownloaded('architecture-diagram'),
    hasVisited('/services/ultra-baja-latencia'),
    hasVisited('/services/ia-soberana'),
    timeOnPage > 10 * 60 // 10 minutos
  ]
};

if (personaSignals.isCTO.filter(x => x).length >= 3) {
  // Show CTO-specific content
  showSection('cto-case-studies');
  showCTA('agendar-sesion-tecnica');
  hideSection('quick-pricing');
}
```

**Content Personalization:**
- ✅ Mostrar benchmarks técnicos (no marketing)
- ✅ Enfatizar seguridad + control (no cost)
- ✅ CTAs: sesión técnica, arquitectura detallada (no "contact sales")
- ✅ Case studies: métrica de latencia, arquitectura antes/después

---

## ICP #2: CFO (Business Decision-Maker)

### Perfil Demográfico

**Título:** Chief Financial Officer / VP Finance / Controller

**Industria:** Fintech, banca, gobierno, healthcare, retail

**Empresa:** Midmarket (200-2000 empleados) a Enterprise (2000+)

**Región:** Latinoamérica

**Edad:** 40-60 años

**Experience:** 20+ años finance, 7+ años en CFO-equivalent

### Psicografía (Motivaciones Profundas)

**Motivaciones Intrínsecas:**
- Reducir OPEX sin sacrificar performance
- Mitigar riesgos regulatorios (multas, auditorías)
- Mejorar retención de talento (mejor herramientas)
- Acelerar time-to-market sin inversión heroica

**Miedos/Objections:**
- "¿Cuál es el ROI realmente?" (payback unclear)
- "¿Cuánto va a costar esto en overruns?" (budget risk)
- "¿Esto es necesario o es tech por tech?" (business alignment)
- "¿Reguladores van a aceptar la arquitectura?" (compliance risk)

**Valores:**
- Números concretos (no promesas vagas)
- Transparencia de costos (fixed-price, hito-based)
- Cumplimiento regulatorio (zero surprises)
- Business alignment (tech serve to business, not vice versa)

### Pain Points Específicos (Rank by Business Impact)

1. **OPEX cloud fuera de control** (presupuesto IT +40% YoY sin ROI claro)
   - Síntoma: "Factura AWS se duplicó sin growth"
   - Causa raíz: Arquitectura ineficiente (Java legacy), overprovisioning
   - Impact: IT presupuesto compite con innovación, shareholder ROI down

2. **Riesgo regulatorio = multas potenciales** (SPDP Ecuador = 1% ingresos)
   - Síntoma: "Regulador preguntó sobre datos, nos falta documentación"
   - Causa raíz: No hay auditoría automática, proceso manual
   - Impact: Sorpresa en inspección = multa catastrófica

3. **Talento técnico escaso/caro** (senior engineers huyen, junior no retienen)
   - Síntoma: "No podemos contratar SRE buenos, costo 2x más que hace 2 años"
   - Causa raíz: Deuda técnica + herramientas anticuadas = frustración
   - Impact: Churn, costo de hiring, reduced velocity

4. **Capacidad de IA limitada** (no puedo competir con FANG)
   - Síntoma: "Competencia está usando IA, nosotros rezagados"
   - Causa raíz: Datos fragmentados, arquitectura no list for IA
   - Impact: Market share loss, customer churn

5. **Auditoría compliance consume recursos** (6+ meses, $500K+ costo)
   - Síntoma: "Auditoría interna toma 6 meses, recursos tied up"
   - Causa raíz: Manual processes, no data lineage
   - Impact: Finance team burnout, delayed closing, audit surprises

### Buyer Journey (CFO)

#### Stage 1: Awareness (Financial Pain, Not Technical)

**Triggers:**
- Lee artículo blog "SPDP Ecuador: Riesgo de Multa de $5-50M" (SEO)
- CFO peer recomenda (network)
- LinkedIn post "Cloud costs reduction" (paid)
- Board meeting: "¿Por qué IT presupuesto crece pero velocity no?"

**CFOs necesitan:**
- Quantified pain: "Esto te cuesta $X"
- Competitive pressure: "Tus competidores ya lo hicieron"
- Simplicity: "No quiero aprender tech, solo resultados"

**Website Experience (Awareness Stage):**
```html
<!-- Hero: Business + Fear -->
<h1>Reducir Costos IT en 50% sin Sacrificar Performance. 
  Otros CFOs ya lo hicieron.</h1>

<!-- Pain Point Section: Business Language -->
<section id="cfo-pain-points">
  <h2>Los 3 Riesgos Financieros de tu IT en 2026</h2>
  <article>
    <h3>Riesgo 1: OPEX Cloud +50% anual sin growth</h3>
    <p>Costo promedio cliente antes: $2.5M/año OPEX cloud</p>
    <p>Costo después de optimización: $1.5M/año (40% reducción)</p>
    <p><strong>Ahorro neto: $1M/año × 5 años = $5M</strong></p>
  </article>
  
  <article>
    <h3>Riesgo 2: Multas regulatorias hasta 1% ingresos</h3>
    <p>SPDP Ecuador obligatorio 2026. Incumplimiento = hasta 1% volumen.</p>
    <p>Para empresa $500M revenue: multa potencial $5M</p>
    <p><strong>Mitigación: Auditoría automática = $150K, ROI 33x</strong></p>
  </article>
</section>

<!-- CFO Authority: ROI Data -->
<section id="cfo-metrics">
  <h3>Resultados de Otros CFOs</h3>
  <div>
    <p><strong>85%</strong> reducción tiempo auditoría</p>
    <p><strong>40-60%</strong> reducción OPEX cloud</p>
    <p><strong>3-6 meses</strong> payback</p>
  </div>
</section>

<!-- CTA: CFO Specific -->
<button>Descargar ROI Calculator (5 min)</button>
```

#### Stage 2: Consideration (ROI Validation, Budget Planning)

**Triggers:**
- Descargó ROI calculator
- Visita sección de pricing 2+ veces
- Visita sección de case studies (buscando validación)
- Comparte artículo SPDP con auditor interno

**CFOs necesitan:**
- ROI cuantificado específico a su empresa (no genérico)
- Budget confidence: fixed-price, hito-based
- Regulatory certainty: "Esto me hace compliant 100%"

**Website Experience (Consideration Stage):**
```html
<!-- ROI Calculator Interactive -->
<section id="roi-calculator">
  <h3>¿Cuál es tu potencial ahorro?</h3>
  <form>
    <label>
      Current monthly AWS bill:
      <input type="number" name="aws-cost" placeholder="$50,000">
    </label>
    <label>
      Number of Java pods:
      <input type="number" name="java-pods" placeholder="50">
    </label>
    <label>
      IT team headcount:
      <input type="number" name="it-headcount" placeholder="25">
    </label>
    <button type="submit">Calculate Savings</button>
  </form>
  
  <div id="results" style="display:none">
    <p>Your estimated annual savings: <strong>$XXX,XXX</strong></p>
    <p>Payback period: <strong>6-9 months</strong></p>
    <p>5-year NPV: <strong>$X.XM</strong></p>
  </div>
</section>

<!-- Case Studies: Business Metrics -->
<section id="case-studies-business">
  <article>
    <h3>Financial Services: OPEX Optimization</h3>
    <p>Investment: $350K | Timeline: 12 months</p>
    <ul>
      <li>Cloud cost reduction: 40% ($500K/year)</li>
      <li>Team productivity: +20% (15 FTE saved)</li>
      <li>Payback: 8 months</li>
      <li>5-year NPV: $1.8M</li>
    </ul>
  </article>
</section>

<!-- Compliance: CFO Language -->
<section id="compliance-cfo">
  <h3>Regulatory Risk Mitigation</h3>
  <p>SPDP Ecuador = up to 1% revenue in fines.</p>
  <p>Our audit automation: 100% population analysis, 2-week turnaround.</p>
  <p>CFO benefit: Zero surprises, zero risk, audit cost -85%.</p>
</section>

<!-- CTA: Proposal -->
<button>Request CFO-specific proposal</button>
```

#### Stage 3: Decision (Budget Approved, Contracting)

**Triggers:**
- Approved budget internally
- Requesting RFP
- Asking for references (other CFOs)
- Asking about contract terms (SLA, liability)

**CFOs necesitan:**
- Legal certainty (clear contracts, no hidden costs)
- Reference checks (talk to other CFOs)
- Implementation timeline (minimum disruption to finance close)

**Website Experience (Decision Stage):**
```html
<!-- Pricing for CFO -->
<section id="pricing-cfo">
  <h3>Investment Breakdown (Fixed Price, Hito-Based)</h3>
  <article>
    <h4>Modernización Legacy + Rust Optimization</h4>
    <p class="price">$350K USD</p>
    <ul class="timeline">
      <li>Months 1-3: Architecture design ($100K paid)</li>
      <li>Months 4-9: Implementation ($150K paid)</li>
      <li>Months 10-12: Production + training ($100K paid)</li>
    </ul>
    <p class="roi"><strong>Expected ROI: 40% OPEX reduction ($500K/year)</strong></p>
  </article>
</section>

<!-- References: CFO to CFO -->
<section id="references">
  <h3>Reference Customers</h3>
  <p>"I saved $600K/year in cloud costs. This paid for itself in 7 months."</p>
  <p class="author">— CFO, Fintech, Ecuador</p>
  
  <button>Schedule CFO reference call</button>
</section>

<!-- Contract Terms -->
<section id="terms-sla">
  <h3>Contract Terms</h3>
  <ul>
    <li><strong>SLA:</strong> 99.9% uptime on deployed infrastructure</li>
    <li><strong>Support:</strong> 12 months included, no surprise costs</li>
    <li><strong>Penalty clause:</strong> If project overruns >20%, da-tica absorbs cost</li>
    <li><strong>ROI guarantee:</strong> If savings don't materialize, we credit next engagement</li>
  </ul>
</section>

<!-- CTA: Legal/Signature -->
<button>Review contract & schedule legal review</button>
```

### Objection Handling (CFO-Specific)

| Objection | Root Cause | Counter |
|-----------|-----------|---------|
| "How do I know ROI is real?" | Skepticism, past disappointments | Share benchmarks, references, pilot-based engagement |
| "What if this delays our close?" | Finance close timeline critical | Minimal IT disruption, phased rollout, no quarter-end blackout |
| "What hidden costs?" | Prior bad experiences | Fixed-price contract, itemized roadmap, no T&M |
| "Can't we just use cloud native?" | Sunk cost in cloud | We work WITH cloud, not against it; optimize existing spend |
| "What if your team abandons us?" | Vendor exit risk | Full documentation, knowledge transfer, code in your repo |

### Personalization Triggers (Website)

Detectar CFO automáticamente:
```javascript
// CFO Detection
const cfoPerson = {
  signals: [
    hasVisited('/articles/spdp-ecuador-multas'),
    hasVisited('/articles/cloud-cost-optimization'),
    hasDownloaded('roi-calculator'),
    hasVisited('/pricing'),
    hasVisited('/case-studies'),
    timeOnPage > 8 * 60 // 8 minutos en secciones business
  ]
};

if (cfoPerson.signals.filter(x => x).length >= 3) {
  // Show CFO-specific content
  showSection('cfo-roi-metrics');
  showCTA('descargar-propuesta-cfo');
  showCTA('agendar-llamada-cfo-peer');
  hideCTA('agendar-sesion-tecnica'); // Hide for CFO
  hideSection('technical-benchmarks');
  showSection('case-studies-business-metrics');
}
```

**Content Personalization:**
- ✅ Mostrar números, ROI, payback (no marketing)
- ✅ Enfatizar riesgos regulatorios (no tech)
- ✅ CTAs: descargar calculador ROI, agendar llamada peer (no "tech deep dive")
- ✅ Case studies: métricas de negocio (OPEX, headcount savings), no arquitectura

---

## Buyer Journey Convergence (Both Personas)

Después de Stage 3 (Decision), ambos personas convergen en:

### Stage 4: Implementation (Shared Outcome Focus)

**Both CTO & CFO necesitan:**
- Transparent project governance (weekly standups, burndown)
- Risk mitigation (no surprises)
- Knowledge transfer (they own it after da-tica leaves)

**Website Experience (Post-Signature):**
```html
<!-- Implementation Kickoff Package -->
<section id="implementation">
  <h3>Your Implementation Timeline</h3>
  <ul>
    <li>Week 1: Team onboarding + environment setup</li>
    <li>Week 2-4: Architecture design + signoff</li>
    <li>Week 5-16: Development + testing</li>
    <li>Week 17-20: Canary deployment + production rollout</li>
    <li>Week 21-24: Knowledge transfer + SLA handoff</li>
  </ul>
  
  <button>Access project management portal (Jira/Linear)</button>
</section>
```

---

## Implementation: Website Persona Detection

```html
<!-- Hero Section: Persona Selection (Option) -->
<section id="hero-persona-picker">
  <h2>I'm a:</h2>
  <button data-persona="cto">CTO / Technical Leader</button>
  <button data-persona="cfo">CFO / Business Leader</button>
  <button data-persona="both">Both (curious)</button>
</section>

<!-- Persistent Content Customization -->
<script>
const selectedPersona = localStorage.getItem('persona') || detectPersona();

// Hide/show sections based on persona
document.querySelectorAll('[data-persona-cto]').forEach(el => {
  el.style.display = (selectedPersona === 'cto' || selectedPersona === 'both') ? 'block' : 'none';
});

document.querySelectorAll('[data-persona-cfo]').forEach(el => {
  el.style.display = (selectedPersona === 'cfo' || selectedPersona === 'both') ? 'block' : 'none';
});

function detectPersona() {
  // Behavioral signals
  if (visitedPages.includes('/blog/rust') && visitedPages.includes('/architecture')) return 'cto';
  if (visitedPages.includes('/roi-calculator') && visitedPages.includes('/pricing')) return 'cfo';
  return 'both'; // default
}
</script>
```

---

## Checklist de Implementación

- [ ] Crear 2 hero variants (CTO vs CFO message)
- [ ] Implementar persona detection (JS behavioral)
- [ ] Crear CTA variants (CTO: "agendar sesión técnica", CFO: "descargar ROI calculator")
- [ ] Content sections: marcar con `data-persona-*`
- [ ] Case studies: 50% technical metrics, 50% business metrics
- [ ] FAQ: separar CTO questions vs CFO questions
- [ ] Testing: A/B personas, track conversion by persona
- [ ] Tracking: Google Analytics event "persona_selected", conversiones por persona
- [ ] Email flows: personalized based on persona (CTO gets tech deep-dives, CFO gets ROI updates)

---

## Success Metrics by Persona

| KPI | CTO Target | CFO Target |
|-----|------------|-----------|
| Blog dwell time | 10+ min | 6+ min |
| Download rate | 15-20% (benchmarks, architecture) | 20-25% (ROI calc, case studies) |
| CTA conversion | 5-8% (book session) | 8-12% (download proposal) |
| Sales cycle | 60-90 days | 45-60 days |
| Deal size | $100K-500K | $150K-600K |
| Win rate | 35-45% | 50-65% |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chris78rey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
