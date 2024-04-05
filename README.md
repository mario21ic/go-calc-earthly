# go-calc
Files to practice golang

List targets:
```
earthly ls
```

Lint:
```
earthly +lint
```

Cache deps:
```
earthly +deps
```

Tests:
```
earthly +tests
earthly --no-cache +tests
```

Build:
```
earthly +build
earthly +build --version=1.0 # args
```


Docker:
```
earthly +docker
earthly +docker --tag="nuevo"
earthly --push +docker # para enviar al docker hub
```

Run all:
```
earthly +all
```

Extra - Multi arch:
```
earthly --platform=linux/amd64 +build --version=1.2
file build/go-calc

earthly --platform=linux/arm64 +build --version=1.3
file build/go-calc

# Docker images
earthly --platform=linux/arm64 +docker
earthly --platform=linux/amd64 +docker
```

Extra - Running Docker:
```
earthly -P +hello
earthly -P +hello # cache en accion
earthly -P --no-cache +hello
```

## CI tools examples:
Jenkins:
```
cat Jenkinsfile
```

Github actions:
```
cat .github/workflows/ci.yaml
```

