---
name: extension-marketing-content
description: Creates compelling marketing copy for SketchUp extensions targeting architects, designers, and 3D modelers. Use when writing product descriptions, landing page copy, or marketing materials for ML Extensions.
metadata:
  author: mostafalamey
---

# Extension Marketing Content Writer

Create persuasive, benefit-focused marketing content that converts visitors into customers.

## Target Audience Personas

### Primary: Architectural Professionals
- **Pain Points**: Time-consuming modeling, repetitive tasks, client deadline pressure
- **Goals**: Faster workflows, professional results, client satisfaction
- **Language**: Professional, time-focused, ROI-driven

### Secondary: Interior Designers
- **Pain Points**: Complex furniture modeling, space planning challenges
- **Goals**: Realistic visualizations, efficient design process
- **Language**: Creative, aesthetic-focused, solution-oriented

### Tertiary: Hobbyist 3D Modelers
- **Pain Points**: Learning curve, limited time, budget constraints
- **Goals**: Easy-to-use tools, learning resources, affordable solutions
- **Language**: Accessible, educational, value-focused

## Content Templates

### Hero Section Headlines
```
Power Headlines for ML Kitchens:
• "Design Professional Kitchens in Minutes, Not Hours"
• "Transform Your Kitchen Modeling Workflow Forever"
• "The Extension That Makes Kitchen Design Effortless"

Power Headlines for ML Doors:
• "Create Perfect Door & Window Openings Instantly"
• "Stop Fighting with Door Placement - Let ML Doors Handle It"
• "Professional Door Library at Your Fingertips"

Universal Formulas:
• "[Action Verb] [Benefit] in [Time Frame]"
• "The Only [Category] Extension You'll Ever Need"
• "Finally, [Desired Outcome] Without [Current Pain Point]"
```

### Feature Descriptions (Benefit-Focused)

#### Instead of: "Automated kitchen cabinet generation"
#### Write: "Generate complete kitchen layouts with cabinets, countertops, and appliances in under 5 minutes - saving you hours of tedious modeling work."

```jsx
const FeatureBenefits = {
  technical: "Automated kitchen cabinet generation with parametric controls",
  marketing: "Design complete kitchens with cabinets, countertops, and appliances in minutes - not hours",
  benefit: "Save 3-5 hours per kitchen project while ensuring professional accuracy"
};
```

### Value Propositions

#### For ML Kitchens:
```
Primary: "Complete kitchen design automation - from concept to 3D model in minutes"
Supporting: 
• Reduces kitchen modeling time by 80%
• Includes 200+ cabinet styles and configurations
• Ensures accurate measurements and manufacturer specs
• Works with your existing SketchUp workflow
```

#### For ML Doors:
```
Primary: "Perfect door and window placement with zero measurement errors"
Supporting:
• Automatically creates proper openings in walls
• Includes extensive door/window library
• Handles complex angles and custom sizes
• One-click installation and removal
```

### Social Proof Templates

```jsx
const TestimonialFormat = ({ name, title, company, quote, project }) => (
  <div className="testimonial">
    <blockquote>
      "{quote}"
    </blockquote>
    <cite>
      <strong>{name}</strong>, {title} at {company}
      <small>Used for: {project}</small>
    </cite>
  </div>
);

// Example content:
const testimonials = [
  {
    name: "Sarah Chen",
    title: "Senior Architect", 
    company: "Design Studio Pro",
    quote: "ML Kitchens cut our kitchen design time from 4 hours to 30 minutes. Our clients love the realistic visualizations.",
    project: "Luxury residential development"
  }
];
```

### Pain Point Amplification

#### Before/After Scenarios:
```
❌ BEFORE (Without ML Extensions):
• Spend hours placing individual cabinets
• Struggle with accurate measurements  
• Redo work when client wants changes
• Miss deadlines due to modeling time

✅ AFTER (With ML Extensions):
• Complete kitchen layouts in minutes
• Automatic precision and measurements
• Instant design variations for clients  
• Meet every deadline with time to spare
```

### Call-to-Action Copy

#### Urgency-Driven CTAs:
```
• "Start Saving Hours Today - Get ML Kitchens Now"
• "Join 5,000+ Architects Using ML Extensions"
• "Download Now - 30-Day Money-Back Guarantee"
• "Stop Wasting Time - Automate Your Workflow"
```

#### Benefit-Focused CTAs:
```
• "Get Professional Results in Minutes"
• "Transform Your Design Process Now"
• "See Why Architects Choose ML Extensions"
• "Unlock Your Design Potential Today"
```

### Objection Handling Copy

#### Common Objections & Responses:

**"Is it compatible with my workflow?"**
→ "ML Extensions integrates seamlessly with SketchUp - no workflow changes required. Works with your existing templates, styles, and plugins."

**"Will it look too generic?"**
→ "Choose from 200+ customizable styles or create your own. Every design is uniquely yours, just created 10x faster."

**"Is it worth the cost?"**
→ "Pay for itself after one project. Save 3-5 hours per kitchen at $50/hour = $150-250 value vs $XX investment."

**"What if I need support?"**
→ "Get direct email support, video tutorials, and lifetime updates included. Plus 30-day money-back guarantee."

## Email Marketing Sequences

### Welcome Series (3-email sequence):

#### Email 1: Welcome + Quick Win
```
Subject: Your ML Kitchen Extension is ready! (+ Quick start guide)

Hi [Name],

Welcome to the ML Extensions family! 🎉

Your download link: [LINK]

Get started in 5 minutes:
1. Install the .rbz file in SketchUp
2. Find ML Kitchens in the Extensions menu  
3. Create your first kitchen layout

Watch the 2-minute tutorial: [TUTORIAL_LINK]

Need help? Hit reply - I personally answer every email.

Best,
[Your Name]
```

#### Email 2: Success Stories (3 days later)
```
Subject: How Sarah saved 4 hours on her last project

Hi [Name],

Quick question: Have you tried ML Kitchens yet?

If not, here's some inspiration...

Sarah Chen (architect in Seattle) told me:

"I used to spend entire afternoons just placing cabinets. Now I complete full kitchen designs during my lunch break. My clients think I'm magic."

Her secret? She follows this simple 3-step process:

1. Set room dimensions
2. Choose cabinet style
3. Click generate

That's it. Professional results in minutes.

Ready to try it? [TUTORIAL_LINK]

Best,
[Your Name]
```

## Landing Page Copy Structure

### Above the Fold:
```
Headline: [Benefit-driven promise]
Sub-headline: [Elaborate on benefit + time frame]
Hero image: [Extension in action]
Primary CTA: [Action-oriented button]
Social proof: [Number of users or testimonial]
```

### Features Section:
```
"Everything You Need for Professional Kitchen Design"

✅ Feature 1: [Capability] → [Benefit] → [Time saved]
✅ Feature 2: [Capability] → [Benefit] → [Quality improvement]  
✅ Feature 3: [Capability] → [Benefit] → [Cost savings]
```

### Closing Section:
```
"Ready to Transform Your Design Process?"

[Recap key benefits]
[Address final objection]
[Strong CTA]
[Guarantee/Risk reversal]
```

## Content Guidelines

### Voice & Tone:
- **Professional yet approachable** - You're speaking to skilled professionals
- **Confident but not arrogant** - You solve real problems
- **Benefit-focused** - Always lead with what's in it for them
- **Time-conscious** - These are busy people, respect their time

### Power Words to Use:
- Transform, Professional, Instantly, Effortless
- Automated, Precision, Workflow, Solution
- Save, Fast, Simple, Powerful, Complete

### Words to Avoid:
- Cheap, Basic, Simple (implying low quality)
- Try, Maybe, Might, Possibly
- Technical jargon without explanation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mostafalamey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
