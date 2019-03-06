### gotests
---
https://github.com/cweill/gotests

```go
// https://github.com/cweill/gotests/blob/master/gotests.go
package gotests

import ()

type Options struct {
  Only *regexp.Regexp
  Exclude *regexp.Regexp
  Exported bool
  PrintInputs bool
  Subtests bool
  Importer func() types.Importer
  TemplateDir string
}

type GeneratedTest struct {}

func GenerateTests(srcPath string, opt *Options) ([]*GeneratedTest, error) {
  if opt == nil {}
  srcFiles, err := input.Files(srcPath)
  if err != nil {}
  if opt.Importer == nil || opt.Importer() == nil {}
  return parallelize(srcFiles, files, opt)
}

type result struct {}

func parallelize(srcFiles, files []models.Path, opt *Options) ([]*GeneratedTest, error) {

}

func readResult() () {}

func generatedTet() () {}

func parseTestFile() () {}

func testableFuncs() () {}

func isInvalid() bool {}

func isTestFunction() bool {}

func isExecluded () bool {}

func isIncluded() bool {}

func conatains(ss []string, s string) bool {
  if i := sort.SearchStrings(ss, s); i < len(ss) && ss[i] == s {
    return true
  }
  return false
}


```

```
```

```
```


