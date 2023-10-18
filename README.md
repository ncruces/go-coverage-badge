# Go coverage report

A GitHub Action to add a coverage [report][1], [badge][2], and [chart][4] to your Go repo.

Apply it to your repo by adding this step to one of your workflows:

```yaml
- name: Update coverage report
  uses: ncruces/go-coverage-report@v0
```

Your repo needs to have a Wiki for the action to work,
and workflows need to have read _and_ write permissions to the repo.

The action has 6 configuration knobs:
- `coverage-file`: optional coverage input file,
  default is to generate coverage for all packages in the current module.
- `badge-style`: optional coverage badge style,
  generated by [shields.io](https://shields.io),
  one of: `flat` (default), `flat-square`, `plastic`, `for-the-badge`, `social`.
- `report`: default `true`,
  generate an [HTML coverage report][1].
- `chart`: default `false`,
  generate an [SVG coverage chart][4].
- `amend`: default `false`,
  amend your Wiki, avoiding a series of “Update coverage” commits.
- `reuse-go`: default `false`,
  reuse Go as setup by the caller action
  (for performance, caching, configurability).

Also, consider:
- running this step _after_ your tests run
  - coverage will fail if any test fails, so you may skip it if they fail
- running it only once per commit
  - use a condition to avoid repeated matrix runs
- skipping it for PRs
  - PRs lack permission to update the Wiki,
    nor would you want unsubmitted PRs to do so
- allowing it to fail without failing the entire job
  - if tests pass, the problem might be with the action itself, not your code

Complete example:

```yaml
- name: Test
  run: go test -v ./...

- name: Update coverage report
  uses: ncruces/go-coverage-report@v0
  with:
    report: true
    chart: true
    amend: true
    reuse-go: true
  if: |
    matrix.os == 'ubuntu-latest' &&
    github.event_name == 'push'  
  continue-on-error: true
```

The action generates an [HTML report][1], [SVG badge][2] and [chart][4],
and saves them as “hidden” files in your Wiki.

To add a coverage badge to your `README.md`, use this Markdown snippet:

```markdown
[![Go Coverage](https://github.com/USER/REPO/wiki/coverage.svg)](https://raw.githack.com/wiki/USER/REPO/coverage.html)
```

Clicking on the badge opens the [coverage report][1].
If you also want to show the [coverage chart][4],
create a [Wiki page][5] and link to it instead.

The action will also [log][3] to the Wiki the unix timestamp and coverage of every run,
so it can generate the [coverage chart][4].

[1]: https://raw.githack.com/wiki/ncruces/go-sqlite3/coverage.html
[2]: https://github.com/ncruces/go-sqlite3/wiki/coverage.svg
[3]: https://github.com/ncruces/go-sqlite3/wiki/coverage.log
[4]: https://github.com/ncruces/go-sqlite3/wiki/coverage-chart.svg
[5]: https://github.com/ncruces/go-sqlite3/wiki/Test-coverage-report

## Credits

- [@vieux](https://github.com/vieux/) for [gocover.io](https://github.com/vieux/gocover.io) which I've used for years before creating this
- [@Prounckk](https://github.com/Prounckk) for the [blog](https://eremeev.ca/posts/golang-test-coverage-github-action/) that prompted this solution
- [raw.githack.com](https://raw.githack.com/) for proxying the HTML report
- [shields.io](https://shields.io/) for SVG badges
- [quickchart.io](https://quickchart.io/) for SVG charts
