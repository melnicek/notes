```
npm init -y
npm install -D typescript @types/node
```

tsconfig.json
```json
{
    "compilerOptions": {
        "lib": ["ES2020"],
        "module": "commonjs",
        "target": "es2019",
        "types": ["node"],
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
    }
}
```
