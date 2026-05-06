# `aihero-tdd:tdd` — what happens after you install it

> 🤝 Greg (human) and Claude (AI) recorded this together — with much respect for Matt Pocock's TDD work.

This file is a **verification record**: a transcript-style capture of what installing `aihero-tdd@variousforks-mattpocock-skills` actually changes in a Claude Code session. Not a tutorial — proof that the marketplace install path works end-to-end and that the skill *meaningfully shapes Claude's behavior*, not just metadata.

## TL;DR

After running:

```text
/plugin marketplace add VariousForks/skills-by-aihero-dev-mattpocock
/plugin install aihero-tdd@variousforks-mattpocock-skills
/reload-plugins
```

…Claude Code in any project will, when you ask for TDD-driven work:

1. **Refuse to write code first.** The skill enforces an interview before any test or implementation appears. It asks about the public interface, the first behavior to drive, and the priority list of subsequent behaviors. (This is the skill's anti-pattern guardrail against "write all tests, then all code.")
2. **Use literal RED / GREEN narration.** Claude will write *one* test, run it, narrate `RED confirmed`, then write the *minimal* implementation, run again, narrate `GREEN`, and only then move to the next slice.
3. **Stay on vertical slices.** One test → one impl → repeat. No bulk-test-writing, no implementation that runs ahead of the test that justifies it.

## Reproduction setup (260506-1142, Claude Code v2.1.131)

The fixture used to verify the skill — kept minimal so the loop is the only thing being tested, not the user's project complexity:

```text
greet/
├── go.mod          # module example.com/greet, Go 1.22
├── Makefile        # `make test` → `go test ./...`
└── README.md       # one-line description
```

`go.mod`:

```go
module example.com/greet

go 1.22
```

`Makefile`:

```makefile
.PHONY: test build clean

test:
	go test ./...

build:
	go build ./...
```

No source file. No tests. Empty starting state.

## The session

**Prompt sent:** `read the readme and start tdd`

**What inner Claude did, in order:**

```text
● Skill(aihero-tdd:tdd)
  ⎿  Successfully loaded skill
```

The skill loaded automatically — based on the trigger phrase "tdd" in the user prompt and the description in `tdd/skills/tdd/SKILL.md` frontmatter. No `--plugin-dir` was used; the skill came purely from the marketplace install on the user's profile.

```text
● TDD skill loaded. Before writing code, per the workflow I need to confirm the plan with you.

  The repo is a greet Go module with no source yet. To start a tracer bullet, I need to know:

  1. Public interface — likely a Greet(name string) string function in package greet. Confirm or specify a different shape?
  2. First behavior — typical tracer: Greet("World") returns "Hello, World!". OK to start there?
  3. Subsequent behaviors to drive incrementally — e.g. empty name → "Hello, stranger!", trim whitespace, locale variants? Pick the ones that matter.
```

This is the skill's interview pattern enforced verbatim — interface, first tracer, priority list. The phrase "tracer bullet" is straight from the SKILL.md ("Vertical slices via tracer bullets. One test → one implementation → repeat.").

After replying *"go ahead with those defaults"*:

### Slice 1: `Greet("World") → "Hello, World!"`

```text
● Starting tracer bullet: RED first.

● Write(greet_test.go)
  package greet

  import "testing"

  func TestGreetWorld(t *testing.T) {
      got := Greet("World")
      want := "Hello, World!"
      if got != want {
          t.Fatalf("Greet(%q) = %q; want %q", "World", got, want)
      }
  }

● Bash(make test)
  ⎿  Error: Exit code 2
     ./greet_test.go:6:9: undefined: Greet
     FAIL  example.com/greet [build failed]

● RED confirmed (undefined: Greet). Now GREEN — minimal impl.

● Write(greet.go)
  package greet

  func Greet(name string) string {
      return "Hello, " + name + "!"
  }

● Bash(make test)
  ⎿  ok  example.com/greet  0.002s

● GREEN. Tracer bullet complete.
```

Note the discipline: minimal impl literally concatenates the parts. No premature handling of the empty-name case, no trim, no nil safety. Each of those will get its own slice when its test arrives.

### Slice 2: `Greet("") → "Hello, stranger!"`

```text
● Next behavior: empty name → "Hello, stranger!". RED:

● [appended TestGreetEmptyNameFallsBackToStranger to greet_test.go]

● Bash(make test)
  ⎿  --- FAIL: TestGreetEmptyNameFallsBackToStranger
     greet_test.go:17: Greet("") = "Hello, !"; want "Hello, stranger!"

● RED. Minimal GREEN:

● Update(greet.go)
  func Greet(name string) string {
      if name == "" {
          return "Hello, stranger!"
      }
      return "Hello, " + name + "!"
  }

● Bash(make test)
  ⎿  ok  example.com/greet  0.002s

● GREEN. Next behavior: trim whitespace ("  Alice  " → "Hello, Alice!", and "   " → stranger). RED:
```

The session was interrupted here — verification complete after two full red-green cycles. The skill was already lining up slice 3 (whitespace) when stopped, which is the expected ongoing-loop behavior.

## Final code (after 2 slices)

`greet.go` (8 lines, all driven by tests):

```go
package greet

func Greet(name string) string {
    if name == "" {
        return "Hello, stranger!"
    }
    return "Hello, " + name + "!"
}
```

`greet_test.go` (2 tests, written one-at-a-time):

```go
package greet

import "testing"

func TestGreetWorld(t *testing.T) {
    got := Greet("World")
    want := "Hello, World!"
    if got != want {
        t.Fatalf("Greet(%q) = %q; want %q", "World", got, want)
    }
}

func TestGreetEmptyNameFallsBackToStranger(t *testing.T) {
    got := Greet("")
    want := "Hello, stranger!"
    if got != want {
        t.Fatalf("Greet(%q) = %q; want %q", "", got, want)
    }
}
```

`make test` → `ok example.com/greet`.

## Why this verification matters

The Claude Code plugin docs say *what* a marketplace install does (registers the catalog, downloads the plugin, namespaces the skill). They don't show that the skill *actually drives Claude's behavior in the right way* once it's loaded. This file is the missing piece: a reproducible record showing the skill changes Claude's coding style — not just the menu.

Three specific behaviors observed that would not happen without the skill:

1. **Interview before code.** A vanilla Claude session asked "build a Greet function" would have written it immediately. The skill caused a pause-and-confirm step.
2. **Vertical slicing.** A vanilla session would likely have written multiple test cases at once. The skill drove exactly one test per cycle.
3. **Explicit RED/GREEN narration.** This vocabulary is from the skill, not Claude's defaults. It's a useful audit trail for someone reviewing the session.

If you replicate this fixture (any empty Go module + a Makefile with `make test`) and run the same prompt, you should see the same shape of session.

## Reproducing this verification

```bash
mkdir -p /tmp/greet && cd /tmp/greet
cat > go.mod <<'EOF'
module example.com/greet
go 1.22
EOF
cat > Makefile <<'EOF'
.PHONY: test
test:
	go test ./...
EOF

claude
# inside Claude Code:
# /plugin marketplace add VariousForks/skills-by-aihero-dev-mattpocock
# /plugin install aihero-tdd@variousforks-mattpocock-skills
# /reload-plugins
# > read the readme and start tdd
```

Expect: `Skill(aihero-tdd:tdd)` invocation → interview questions → one test → red → minimal impl → green → next slice.

## Credits

* Skill content (SKILL.md, tests.md, mocking.md, refactoring.md, interface-design.md, deep-modules.md): **[Matt Pocock / AI Hero](https://www.aihero.dev/)**. The skill, the blog post, and the video at https://www.aihero.dev/skill-test-driven-development-claude-code are the source of the discipline being demonstrated here.
* Plugin packaging (manifest, marketplace catalog, namespace) and this verification record: **Greg (human) + Claude (AI)**, on the fork at https://github.com/VariousForks/skills-by-aihero-dev-mattpocock. Upstream feature request: [mattpocock/skills#138](https://github.com/mattpocock/skills/issues/138).
