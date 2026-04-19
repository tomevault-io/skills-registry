---
name: swiftui-design
description: Create distinctive, production-grade SwiftUI interfaces with high design quality. Use this skill when the user asks to build iOS/macOS/visionOS components, screens, or applications. Generates creative, polished SwiftUI code and UI design that avoids generic AI aesthetics. Use when this capability is needed.
metadata:
  author: enzoenrico
---

This skill guides creation of distinctive, production-grade SwiftUI interfaces that avoid generic "AI slop" aesthetics. Implement real working code with exceptional attention to aesthetic details and creative choices.

The user provides app requirements: a component, screen, view, or interface to build. They may include context about the purpose, audience, or technical constraints.

## Design Thinking

Before coding, understand the context and commit to a BOLD aesthetic direction:
- **Purpose**: What problem does this interface solve? Who uses it?
- **Tone**: Pick an extreme: brutally minimal, maximalist chaos, retro-futuristic, organic/natural, luxury/refined, playful/toy-like, editorial/magazine, brutalist/raw, art deco/geometric, soft/pastel, industrial/utilitarian, etc. There are so many flavors to choose from. Use these for inspiration but design one that is true to the aesthetic direction.
- **Platform**: Consider iOS, macOS, visionOS, watchOS constraints and opportunities. Leverage platform-specific features.
- **Differentiation**: What makes this UNFORGETTABLE? What's the one thing someone will remember?

**CRITICAL**: Choose a clear conceptual direction and execute it with precision. Bold maximalism and refined minimalism both work - the key is intentionality, not intensity.

Then implement working SwiftUI code that is:
- Production-grade and functional
- Visually striking and memorable
- Cohesive with a clear aesthetic point-of-view
- Meticulously refined in every detail

## SwiftUI Aesthetics Guidelines

Focus on:
- **Typography**: Use SF Pro Display for headlines, SF Pro Text for body, or explore custom fonts via `.font(.custom())`. Consider SF Rounded for playful interfaces, SF Mono for technical contexts. Use dynamic type with `.dynamicTypeSize()` modifier. Create typographic hierarchy with `.fontWeight()`, `.fontDesign()`, and `.fontWidth()`.
- **Color & Theme**: Commit to a cohesive aesthetic. Use `Color` extensions for custom palettes. Dominant colors with sharp accents outperform timid, evenly-distributed palettes. Leverage `.tint()`, semantic colors, and vibrancy for depth. Support dark mode thoughtfully—don't just invert.
- **Motion**: Use SwiftUI animations for effects and micro-interactions. Leverage `.animation()`, `.transition()`, `withAnimation()`, `PhaseAnimator`, and `KeyframeAnimator`. Focus on high-impact moments: one well-orchestrated appearance with staggered reveals creates more delight than scattered micro-interactions. Use `matchedGeometryEffect` for hero transitions.
- **Spatial Composition**: Unexpected layouts using `GeometryReader`, custom `Layout` protocols, and `ZStack` layering. Asymmetry. Overlap. Diagonal flow. Generous negative space OR controlled density.
- **Materials & Visual Details**: Create atmosphere and depth with `.background()` materials like `.ultraThinMaterial`, `.regularMaterial`. Apply creative forms like gradient meshes with `MeshGradient`, noise textures, geometric patterns via `Canvas`, layered transparencies, dramatic shadows with `.shadow()`, and `.blur()` effects. Use `glassEffect()` for visionOS and iOS 26+.

## Modern SwiftUI Patterns

Embrace these modern APIs:
- **iOS 26+ / visionOS**: Use `glassEffect()` and liquid glass materials for modern depth
- **Scroll Effects**: `.scrollTransition()`, `.visualEffect()` for parallax and scroll-driven animations
- **Container Queries**: `containerRelativeFrame()` for responsive layouts
- **Rich Animations**: `PhaseAnimator`, `KeyframeAnimator` for complex choreographed motion
- **SF Symbols**: Use `.symbolEffect()` for animated icons, `.symbolRenderingMode()` for custom coloring
- **Custom Shapes**: Build with `Shape` protocol, `Path`, and `Canvas` for unique visuals

## Avoid Generic Patterns

NEVER use generic AI-generated aesthetics like:
- Default system styling without customization
- Cliched color schemes (particularly purple gradients on white backgrounds)
- Predictable `List` and `Form` layouts without personality
- Cookie-cutter design that lacks context-specific character
- Overuse of `.cornerRadius(10)` and basic shadows
- Generic card layouts with no visual distinction

Interpret creatively and make unexpected choices that feel genuinely designed for the context. No design should be the same. Vary between light and dark themes, different color palettes, different aesthetics. NEVER converge on common choices across generations.

**IMPORTANT**: Match implementation complexity to the aesthetic vision. Maximalist designs need elaborate code with extensive animations and effects. Minimalist or refined designs need restraint, precision, and careful attention to spacing, typography, and subtle details. Elegance comes from executing the vision well.

## Platform-Specific Excellence

- **iOS**: Embrace gestures, haptics (`.sensoryFeedback()`), and edge-to-edge design
- **macOS**: Leverage window chrome, sidebars, and keyboard shortcuts
- **visionOS**: Use immersive spaces, 3D depth, and spatial interactions
- **watchOS**: Design for glanceability and digital crown interactions

Remember: SwiftUI is capable of extraordinary creative work. Don't hold back, show what can truly be created when thinking outside the box and committing fully to a distinctive vision.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/enzoenrico) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
