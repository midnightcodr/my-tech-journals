### Why
If for any reason the ecosystem.config.js file is updated, here are a couple of ways to ensure the new settings become effective:

### The old way (not recommended)
```bash
pm2 delete myapp
pm2 start ecosystem.config.js
```

### The right way
```bash
pm2 reload ecosystem.config.js
```
