## In NestJS,

### Unit Test
- We write **unit test** mainly for just two files which reside in respective modular in `/src`
		- 1. controller: `name.controler.spec.ts`
		- 2. service: `name.service.spec.ts`
- To execute unit test file:
```bash
  pnpm test -- directory/file_name.ts --watch
```

### Integration Test
- for **Integration Test**, we write test in `/test` directory
		- we create file with suffix `.e2e-spec.ts`
- To execute integration test file:
```bash
  pnpm test:e2e -- directory/file_name.ts --watch
```
- To execute all test file related to current project:
```bash
    pnpm test
```