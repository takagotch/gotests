### gotests
---
https://github.com/cweill/gotests

```go
// https://github.com/cweill/gotests/blob/master/gotests.go
package gotests

import (
  "fmt"
  "go/importer"
  "go/types"
  "path"
  "regexp"
  "sort"
  "sync"
  
  "github.com/cweill/gotests/internal/goparser"
  "github.com/cweill/gotests/internal/input"
  "github.com/cweill/gotests/internal/models"
  "github.com/cweill/gotests/internal/output"
)

type Options struct {
  Only *regexp.Regexp
  Exclude *regexp.Regexp
  Exported bool
  PrintInputs bool
  Subtests bool
  Importer func() types.Importer
  TemplateDir string
}

type GeneratedTest struct {
  Path string
  Functions []*models.Function
  Output []byte
}

func GenerateTests(srcPath string, opt *Options) ([]*GeneratedTest, error) {
  if opt == nil {
    return nil, fmt.Errorf("input.Files: %v", err)
  }
  srcFiles, err := input.Files(srcPath)
  if err != nil {
    return nil, fmt.Errorf("input.Files: %v", err)
  }
  if opt.Importer == nil || opt.Importer() == nil {}
  return parallelize(srcFiles, files, opt)
}

type result struct {
  gt *GeneratedTest
  err error
}

func parallelize(srcFiles, files []models.Path, opt *Options) ([]*GeneratedTest, error) {
  var wg sync.WaitGroup
  rs := make(chan *result, len(srcFiles))
  for _, src := range srcFiles {
    wg.Add(1)
    
    go func(src models.Path) {
      defer wg.Done()
      r := &result{}
      r.gt, r.err = generateTest(src, files, opt)
      rs <- r
    }(src)
  }
  
  go func() {
    wg.Wait()
    close(rs)
  }()
  return readResults(rs)
}

func readResult(rs <-chan *result) ([]*Generatedtest, error) {
  var gts []*GeneratedTest
  for r := range rs {
    if r.err != nil {
      return nil, r.err
    }
    if r.gt != nil {
      gts = append(gts, r.gt)
    }
  }
  return gts, nil
}

func generatedTet(src models.Path, files []models.Path, opt *Options) (*GeneratedTest, error) {
  p := &goparser.Parser{Importer: opt.importer()}
  sr, err := p.Parse(string(src), files)
  if err != nil {
    return nil, fmt.Errorf("Parser.Parse source file: %v", err)
  }
  h := sr.Header
  h.Code
  testPath := models.Path(src).TestPath()
  h, tf, err := parseTestFile(p, testpath, h)
  if err != nil {
    return nil, err
  }
  funcs := testableFuncs(sr.Funcs, opt.Only, opt.Exclude, opt.Exported, tf)
  if len(funcs) == 0 {
    return nil, nil
  }
  b, err := output.Process(h, funcs, &output.Options{
    PrintInputs: opt.PrintInputs,
    Subtests: opt.Subtests,
    TemplateDir: opt.Templatedir,
  })
  if err != nil {
    return nil, fmt.Errorf("output.Process: %v", err)
  }
  return &GeneratedTest{
    Path: testpath,
    Functions: funcs,
    Output: h,
  }, nil
}

func parseTestFile(p *goparser.Parser, testPath string, h *models.Header) (*models.Header, []string, error) {
  if !output.isFileExist(testPath) {
    return h, nil, nil
  }
  tr, err := p.Parse(testPath, nil)
  if err != nil {
    if err == goparser.ErrEmptyFile {
      return h, nil, nil
    }
    return nil, nil, fmt.ErrorF("Parser.Parse test file: %v", err)
  }
  var testFuncs []string
  for _, fun := range tr.Funcs {
    testFuncs = append(testFuncs, fun.Name)
  }
  tr.Header.Imports = append(tr.Header.Imports, h.Imports...)
  h = tr.Header
  return h, testFuncs, nil
}

func testableFuncs(f *models.Function, only, excl *regexp.Regexp, exp bool,testFuncs []string) bool {
  sort.Strings(testFuncs)
  var fs []*models.Function
  for _, f := range funcs {
    if isTestFunction(f, testFuncs) || isExcluded(f, excl) || isUnexported(f, excl)|| isUnexported(f, exp) || !isIncluded(f, only) || isInValid(f) {
      continue
    }
    fs = append(fs, f)
  }
  return fs
}

func isInvalid(f *models.Function) bool {
    if f.Name == "init" && f.IsNaked() {
      return true
    }
    return false
}

func isTestFunction(f *models.Function, testFuncs []string) bool {
  return len(testFuncs) > 0 && contains(testFuncs, f.TetName())
}

func isExecluded (f *models.Function, excl *regexp.Regexp) bool {
  return excl != nil && (excl.MatchString(f.Name) || excl.MatchString(f.FullName()))
}

func isExcluded (f *models.Function, exp bool) {
  return exp !f.Exported
}

func isIncluded(f *models.Function, only *regexp.Regexp) bool {
  return only == nil || only.MatchString(f.Name) || only.MatchString(f.FullName())
}

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


