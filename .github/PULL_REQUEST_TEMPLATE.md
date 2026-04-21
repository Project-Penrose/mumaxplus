## Summary
<!-- What does this PR do? Link to the issue/ticket if applicable. -->

## Type of change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Refactor
- [ ] Documentation
- [ ] CI/CD

## Testing
- [ ] Unit tests pass (`pytest tests/ -v`)
- [ ] Integration tests pass (if applicable)
- [ ] `kustomize build overlays/prod` exits 0 (if k8s changes)
- [ ] `lake build` exits 0 with sorry=0 (if Lean changes)

## Checklist
- [ ] No `ubuntu-latest` runners (use `magnon-enterprise-runners`)
- [ ] No Helm (use Kustomize + ArgoCD)
- [ ] No public registry publishing
- [ ] Copyright header present on new files
- [ ] Alerts route to Google Chat (not Slack)

## Wave / batch
<!-- If part of a wave: Wave NNN, Agent N.N -->
