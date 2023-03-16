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
