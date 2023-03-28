```ts
import { useState, useEffect } from "react";
import { useInView } from "react-intersection-observer";

interface LazyImageProps {
  src: string;
  alt: string;
}

const LazyImage = ({ src, alt }: LazyImageProps) => {
  console.log("src: ", src)
  console.log("alt: ", alt)
  const [loaded, setLoaded] = useState(false);
  const [error, setError] = useState(false);
  const { ref, inView } = useInView({
    threshold: 0, // khi ảnh hiển thị trong viewport (threshold = 0), sẽ bắt đầu tải ảnh
    triggerOnce: true, // chỉ tải ảnh một lần
  });

  useEffect(() => {
    if (inView) {
      const image = new Image();
      image.src = src;
      image.onload = () => {
        setLoaded(true);
      };
      image.onerror = () => {
        setError(true);
      };
    }
  }, [inView, src]);

  return (
    <div ref={ref}>
      {error && <span>Failed to load image</span>}
      {!loaded && !error && <span>Loading image...</span>}
      {loaded && <img src={src} alt={alt} />}
    </div>
  );
};

export default LazyImage;
```

```html
<div>
  <LazyImage src="/next.svg" alt="My Image" />
  <p>Some other content on my page.</p>
</div>
```
```yml
name: CI

on:
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2
    - name: Use Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '14.x'
    - name: Install dependencies
      run: yarn install
    - name: Lint
      run: yarn lint
    - name: Test
      run: |
        yarn test:coverage
        if [ $(echo "$(jq '.total.lines.total' coverage/coverage-summary.json) > 50" | bc -l) -eq 0 ]
        then
          echo "Test coverage below 50%"
          exit 1
        fi
    - name: Build
      run: yarn build
    - name: Upload code coverage report
      uses: codecov/codecov-action@v2.1.0
      with:
        token: ${{ secrets.CODECOV_TOKEN }}

```
