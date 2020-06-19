## Providing macOS software used on CRAN for CI Systems and Local Use

The repository contains a way to re-create the software available on CRAN's
macOS build systems within the [GitHub Actions for R](https://github.com/r-lib/actions) 
workflow for [Continuous Integration (CI)](https://en.wikipedia.org/wiki/Continuous_integration).

To accomplish this, we add a single step to the [standard workflow](https://github.com/r-lib/actions/blob/master/examples/check-standard.yaml)
from [`usethis`](https://usethis.r-lib.org/reference/use_github_action.html) 
that activates when the build container is running on macOS. The [new step
downloads](https://github.com/rmacoslib/r-macos-pkg-gha/blob/bfb4e084bf57a06e7decea338d39a52144c2a0d5/.github/workflows/R-CMD-check.yaml#L63-L70) and extracts all pre-made binaries on
the [`libs-4`](https://mac.r-project.org/libs-4/) directory at the 
[R macos developer portal](https://mac.r-project.org/). These binaries were
created using the [`recipes` system](https://github.com/R-macos/recipes) by 
[Simon Urbanek](https://github.com/s-u), who is the official CRAN maintainer 
for macOS.

In short, we added:

```
- name: Install system dependencies for mac
  if: runner.os == 'MacOS'
  run: |
     curl -sL  https://mac.r-project.org/libs-4/ |
     sed -n 's/.*href=\([^>]*x86_64.tar.gz\).*/\1/p' |
     xargs -I % sh -c 'curl https://mac.r-project.org/libs-4/% --output %; sudo tar fxz % -C /usr/local --strip 2' 
```

**Note:** This is an unofficial project not associated with the CRAN team. The
goal here is to show how to recreate on a CI system the binaries available on
CRAN for testing packages.

## License

MIT 
