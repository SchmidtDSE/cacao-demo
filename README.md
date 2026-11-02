# cacao-demo

A demo for deploying an AI-powered interactive map app with a focus on biodiversity measurements relevant to Peru and cacao.

Users describe in plain language what datasets to show; the app uses an LLM agent with map tools and SQL access to visualize and analyze cloud-native geospatial data.

**App demo:** [https://cacao-demo.nrp-nautilus.io/](https://cacao-demo.nrp-nautilus.io/)

**Feedback form:** [https://data-requests.nrp-nautilus.io/cacao](https://data-requests.nrp-nautilus.io/cacao)

**Full documentation:** [schmidtdse.github.io/glen/](https://schmidtdse.github.io/glen/)

## Repository structure

```
index.html          ← HTML shell — loads core JS/CSS from CDN
layers-input.json   ← which STAC collections to show + LLM settings
system-prompt.md    ← LLM system prompt (customize per app)
k8s/                ← Kubernetes deployment manifests (optional)
```

## More resources

- [Configuration reference](https://schmidtdse.github.io/glen/guide/configuration) — all `layers-input.json` fields with examples
- [Deployment guide](https://schmidtdse.github.io/glen/guide/deployment) — GitHub Pages, Hugging Face Spaces, Kubernetes
- [Core library](https://github.com/SchmidtDSE/glen) — source code for the map, chat, and agent modules
