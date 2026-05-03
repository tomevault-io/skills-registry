---
name: link-core
description: Clone bullet_train-core and link all gems for local development Use when this capability is needed.
metadata:
  author: bullet-train-co
---

# Link Bullet Train Core for Local Development

This skill checks out the `bullet_train-core` repository and links all contained Ruby gems in the Gemfile for local development.

## Steps

1. **Clone the repository** (if not already present):
   - Check if `./local/bullet_train-core` exists
   - If not, create `./local/` directory and clone via SSH:
     ```
     git clone git@github.com:bullet-train-co/bullet_train-core.git ./local/bullet_train-core
     ```

2. **Discover gems in the cloned repo**:
   - List all directories in `./local/bullet_train-core/` that contain a `.gemspec` file
   - These are the gems that need to be linked

3. **Update the Gemfile**:
   - For each bullet_train gem currently using `BULLET_TRAIN_VERSION`, add a `, path: "local/bullet_train-core/<gem_folder>"` option
   - The gem folder name typically matches the gem name (with underscores)
   - Transform lines like:
     ```ruby
     gem "bullet_train", BULLET_TRAIN_VERSION
     ```
     to:
     ```ruby
     gem "bullet_train", BULLET_TRAIN_VERSION, path: "local/bullet_train-core/bullet_train"
     ```

4. **Run bundle install**:
   - After updating the Gemfile, run `bundle install` to link the local gems

## Important Notes

- The SSH URL for the repo is: `git@github.com:bullet-train-co/bullet_train-core.git`
- Keep the `BULLET_TRAIN_VERSION` in place so version compatibility is maintained
- Only modify gem lines that reference `BULLET_TRAIN_VERSION`
- The local path should be relative: `local/bullet_train-core/<gem_name>`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bullet-train-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
