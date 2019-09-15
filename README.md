# Cooperative management

## Setup

Install the dependencies
```
npm install
```

Once the setup is completed you can simply run
```
npm run dev
```

### Known issues:
- The whole contract state is being read into the aepp. This is implemented as a workaround for: https://github.com/aeternity/hackathon-prague/issues/8
- State not being updated after transactions
