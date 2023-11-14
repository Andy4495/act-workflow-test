# act-workflow-test

**Note: This issue has been [fixed][1], [merged][2015], and [released][8].**

---

Small repo with with a minimal example to demonstrate [issue 2003][1] submitted to [`act`][2].

When using [reusable workflows][3] with a [matrix strategy][4], `act` runs all the matrix combinations in a single container, which causes the following error (and therefore quits the test after the first matrix combination is run):

```text
Error: failed to create container: 'Error response from daemon: Conflict. The container name "/act-call-reusable-workflow-reusable-workflow-reusable-workflow-c7535ef5a84abfd1f2412cceca72bd226072ccb44b16a59787972320d9cb1a2d" is already in use by container "d0baad951ea56daf6bd3097af098d08b22d726733e0e4a54120fe40c357874a0". You have to remove (or rename) that container to be able to reuse that name.'
```

See files [`calling-workfow.yml`][6] and [`reusable-flow.yml`][7].

When doing the same thing from within a self-contained workflow (still using a matrix strategy), `act` creates a separate container for each run and does not have any errors. See [`standalone-workflow.yml`][5].

## Root Cause and Fix

In file `pkg/runner/run_context.go`, the function `String()` has a comment about creating a unique name:

```go
func (rc *RunContext) String() string {
    name := fmt.Sprintf("%s/%s", rc.Run.Workflow.Name, rc.Name)
    if rc.caller != nil {
        // prefix the reusable workflow with the caller job
        // this is required to create unique container names
        name = fmt.Sprintf("%s/%s", rc.caller.runContext.Run.JobID, name)
    }
    return name
}
```

However, `rc.caller.runContext.Run.JobID` is not a unique name. Instead, `rc.caller.runContext.Name` should be used. It has an identifier (`-1`, `-2`, etc.) appended to the end of the name by function `NewPlanExecutor()` in file `pkg/runner/runner.go`, ensuring the name is unique.

So the fixed `String()` function should be:

```go
func (rc *RunContext) String() string {
    name := fmt.Sprintf("%s/%s", rc.Run.Workflow.Name, rc.Name)
    if rc.caller != nil {
        // prefix the reusable workflow with the caller job
        // this is required to create unique container names
        name = fmt.Sprintf("%s/%s", rc.caller.runContext.Name, name)
    }
    return name
}
```

Pull request #[2015][2015] has been merged and and included in release [0.2.54][8].

## License

The software and other files in this repository are released under what is commonly called the [MIT License][100]. See the file [`LICENSE`][101] in this repository.

[1]: https://github.com/nektos/act/issues/2003
[2]: https://github.com/nektos/act
[3]: https://docs.github.com/en/actions/using-workflows/reusing-workflows
[4]: https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs
[5]: ./.github/workflows/standalone-workflow.yml
[6]: ./.github/workflows/calling-workflow.yml
[7]: ./.github/workflows/reusable-flow.yml
[8]: https://github.com/nektos/act/releases/tag/v0.2.54
[2015]: https://github.com/nektos/act/pull/2015
[100]: https://choosealicense.com/licenses/mit/
[101]: ./LICENSE
[//]: # ([200]: https://github.com/Andy4495/act-workflow-test)
