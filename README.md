# act-workflow-test

Small repo with with a minimal example to demonstrate [issue 2003][1] submitted to [`act`][2].

When using [reusable workflows][3] with a [matrix strategy][4], `act` runs all the matrix combinations in a single container, which causes the following error (and therefore quits the test after the first matrix combination is run):

```text
Error: failed to create container: 'Error response from daemon: Conflict. The container name "/act-call-reusable-workflow-reusable-workflow-reusable-workflow-c7535ef5a84abfd1f2412cceca72bd226072ccb44b16a59787972320d9cb1a2d" is already in use by container "d0baad951ea56daf6bd3097af098d08b22d726733e0e4a54120fe40c357874a0". You have to remove (or rename) that container to be able to reuse that name.'
```

See files [`calling-workfow.yml`][6] and [`reusable-flow.yml`][7].

When doing the same thing from within a self-contained workflow (still using a matrix strategy), `act` creates a separate container for each run and does not have any errors. See [`standalone-workflow.yml`][5].

## License

The software and other files in this repository are released under what is commonly called the [MIT License][100]. See the file [`LICENSE`][101] in this repository.

[1]: https://github.com/nektos/act/issues/2003
[2]: https://github.com/nektos/act
[3]: https://docs.github.com/en/actions/using-workflows/reusing-workflows
[4]: https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs
[5]: ./.github/workflows/standalone-workflow.yml
[6]: ./.github/workflows/calling-workflow.yml
[7]: ./.github/workflows/reusable-flow.yml
[100]: https://choosealicense.com/licenses/mit/
[101]: ./LICENSE
[//]: # ([200]: https://github.com/Andy4495/act-workflow-test)
