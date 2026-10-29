## Phần 1: Cơ bản

### 1.1. `any` và `unknown` khác nhau thế nào? Khi nào nên dùng `unknown`?

Trả lời:
- Sự khác biệt:
  - any: Tắt hoàn toàn cơ chế kiểm tra kiểu (type checking) của TypeScript. Biến kiểu any có thể truy cập bất kỳ thuộc tính nào, gọi như một hàm hoặc gán cho bất kỳ kiểu dữ liệu nào khác mà compiler không báo lỗi. Rất dễ dẫn đến lỗi crash ứng dụng ở runtime (TypeError: ... is not a function).
  - unknown: Là kiểu top type an toàn hơn any. Biến kiểu unknown có thể nhận bất kỳ giá trị nào, nhưng TypeScript bắt buộc lập trình viên phải kiểm tra kiểu (Type Narrowing / Type Guard) hoặc ép kiểu tường minh trước khi thực hiện các thao tác (truy cập thuộc tính, gọi hàm, gán sang kiểu cụ thể khác).
- Khi nào nên dùng unknown:
  - Khi xử lý dữ liệu động nhận từ bên ngoài chưa chắc chắn cấu trúc: dữ liệu trả về từ API của bên thứ ba, kết quả của JSON.parse(), dữ liệu đọc từ localStorage.
  - Trong khối bắt lỗi: catch (error: unknown) để ép buộc kiểm tra if (error instanceof Error) trước khi truy cập error.message.
  - Bất cứ khi nào muốn đại diện cho dữ liệu gì cũng được nhưng vẫn giữ tính an toàn về kiểu (type safety).

---

### 1.2. `interface` và `type` khác nhau ở đâu? Em sẽ chọn cái nào và vì sao?

Trả lời:
- Điểm khác nhau:
  1. Declaration Merging: interface có thể khai báo nhiều lần cùng tên trong cùng scope và chúng sẽ tự động hợp nhất (merge) các thuộc tính. type không cho phép khai báo trùng tên (sẽ báo lỗi Duplicate identifier).
  2. Khả năng biểu diễn:
     - type mạnh hơn về biểu diễn kiểu: có thể định nghĩa Union (type Status = 'A' | 'B'), Intersection (&), Primitive alias (type ID = string | number), Tuple, Mapped types, Conditional types.
     - interface chỉ dùng để định nghĩa hình dạng (shape) của Object hoặc Hợp đồng (Contract) cho Class/Function.
  3. Kế thừa: interface dùng từ khóa extends rõ ràng theo tư duy hướng đối tượng (OOP). type dùng toán tử giao kiểu & (Intersection).
  4. Performance của Compiler: TypeScript compiler tối ưu và cache interface tốt hơn type, giúp thời gian build nhanh hơn trên các codebase lớn.
- Lựa chọn & Lý do:
  - Ưu tiên dùng interface: Khi định nghĩa cấu trúc đối tượng (Object shape), Models, DTO, hợp đồng cho Class, hoặc viết thư viện/SDK cho người khác sử dụng (để họ có thể mở rộng thông qua declaration merging).
  - Dùng type: Khi cần làm việc với Union types, Primitives, Tuples, hoặc các Utility/Mapped/Conditional types phức tạp.

---

### 1.3. `null` và `undefined` khác nhau thế nào? Tuỳ chọn `strictNullChecks` có tác dụng gì?

Trả lời:
- Khác nhau:
  - undefined: Đại diện cho trạng thái chưa được gán giá trị hoặc vắng mặt ngoài ý muốn (biến vừa khai báo chưa gán, thuộc tính không tồn tại trong object, hàm không có return).
  - null: Đại diện cho trạng thái rỗng có chủ đích do lập trình viên chủ động gán (ví dụ: currentUser = null biểu thị trạng thái chưa đăng nhập).
- Tác dụng của strictNullChecks:
  - Khi tắt (false): null và undefined được coi là giá trị con của tất cả các kiểu dữ liệu khác (có thể gán null cho number, string), dễ gây ra lỗi runtime kinh điển: Cannot read properties of undefined/null.
  - Khi bật (true - mặc định nằm trong strict: true): null và undefined trở thành các kiểu dữ liệu độc lập. Biến kiểu string sẽ không thể gán null hay undefined trừ khi được khai báo tường minh dưới dạng Union: string | null | undefined. Compiler sẽ bắt buộc kiểm tra tồn tại trước khi truy cập thuộc tính, loại bỏ hoàn toàn các lỗi null pointer phổ biến.

---

### 1.4. Optional chaining `?.` và nullish coalescing `??` dùng để làm gì? Cho ví dụ.

Trả lời:
- Optional Chaining (?.): Dùng để truy cập an toàn vào thuộc tính lồng nhau hoặc gọi phương thức của một object. Nếu giá trị phía trước là null hoặc undefined, biểu thức sẽ dừng ngay lập tức và trả về undefined thay vì quăng lỗi crash chương trình.
- Nullish Coalescing (??): Dùng để cung cấp giá trị mặc định (fallback). Điểm mấu chốt: ?? chỉ fallback khi giá trị vế trái là null hoặc undefined. Nó khác với toán tử || (vốn sẽ fallback với tất cả các giá trị falsy như 0, "", false, NaN).
- Ví dụ:
  ```ts
  interface User {
    profile?: {
      name: string;
      score?: number;
    };
  }

  const user: User = { profile: { name: 'Huy', score: 0 } };

  // 1. Optional chaining
  console.log(user.profile?.name); // "Huy"
  console.log(user.profile?.age);  // undefined (không lỗi)

  // 2. Nullish coalescing vs Logical OR
  const scoreOr = user.profile?.score || 10;   // 10 (SAI vì 0 là điểm số hợp lệ nhưng 0 là falsy)
  const scoreNull = user.profile?.score ?? 10; // 0  (ĐÚNG vì 0 không phải null/undefined)
  ```

---

## Phần 2: Hệ thống kiểu (so sánh với Java)

### 2.1. Structural typing là gì? Cho ví dụ hai kiểu khác tên nhưng vẫn gán được cho nhau. Java xử lý trường hợp này thế nào?

Trả lời:
- Structural Typing (Duck Typing): Là hệ thống kiểu đánh giá sự tương thích dựa trên cấu trúc (hình dạng - shape) của dữ liệu thay vì tên của kiểu dữ liệu. Nếu đối tượng B có đầy đủ các thuộc tính với kiểu dữ liệu mà kiểu A yêu cầu, thì B được phép gán cho A ("Nếu nó đi như vịt và kêu như vịt, thì nó là con vịt").
- Ví dụ trong TypeScript:
  ```ts
  interface Point2D {
    x: number;
    y: number;
  }

  interface Coordinate {
    x: number;
    y: number;
  }

  let p: Point2D = { x: 10, y: 20 };
  let c: Coordinate = p; // HỢP LỆ trong TypeScript dù tên interface khác nhau
  ```
- Java xử lý trường hợp này thế nào:
  - Java sử dụng Nominal Typing (định kiểu dựa trên danh tính/tên khai báo).
  - Trong Java, dù hai class Point2D và class Coordinate có cùng các trường int x và int y, trình biên dịch Java vẫn sẽ báo lỗi biên dịch nếu gán chúng cho nhau, trừ khi một class kế thừa class kia hoặc cùng implement một interface chung được khai báo tường minh.

---

### 2.2. Union type là gì? Với một biến kiểu `string | number`, làm sao biết lúc chạy nó đang là kiểu nào?

Trả lời:
- Union Type: Là kiểu cho phép một biến có thể mang một trong nhiều kiểu dữ liệu khác nhau, biểu diễn bằng ký tự | (ví dụ: string | number).
- Cách nhận biết lúc runtime (Type Narrowing / Type Guards):
  - Sử dụng toán tử typeof trong JavaScript để kiểm tra tại runtime:
  ```ts
  function processValue(val: string | number) {
    if (typeof val === 'string') {
      // Bên trong block này, TypeScript hiểu val chắc chắn là string
      console.log(val.toUpperCase());
    } else {
      // Bên trong block này, TypeScript hiểu val chắc chắn là number
      console.log(val.toFixed(2));
    }
  }
  ```

---

### 2.3. Discriminated union là gì? Viết một ví dụ mô tả trạng thái đơn hàng.

Trả lời:
- Discriminated Union (Tagged Union): Là kỹ thuật kết hợp giữa Union Type và Literal Type trên một thuộc tính chung duy nhất (được gọi là "discriminant" hay "tag"). Thuộc tính này giúp TypeScript tự động phân biệt và thu hẹp kiểu của từng thành phần khi sử dụng trong câu lệnh switch hoặc if-else.
- Ví dụ trạng thái đơn hàng:
  ```ts
  interface OrderPending {
    status: 'PENDING';
    createdAt: Date;
  }

  interface OrderShipping {
    status: 'SHIPPING';
    trackingCode: string;
    estimatedDate: Date;
  }

  interface OrderDelivered {
    status: 'DELIVERED';
    deliveredAt: Date;
    receiverSignature: string;
  }

  interface OrderCancelled {
    status: 'CANCELLED';
    cancelReason: string;
  }

  type OrderState = OrderPending | OrderShipping | OrderDelivered | OrderCancelled;

  function handleOrder(order: OrderState) {
    switch (order.status) {
      case 'PENDING':
        console.log(`Đơn chờ xử lý từ lúc: ${order.createdAt}`);
        break;
      case 'SHIPPING':
        // TypeScript tự động gợi ý thuộc tính trackingCode
        console.log(`Đang giao, mã vận đơn: ${order.trackingCode}`);
        break;
      case 'DELIVERED':
        console.log(`Đã giao lúc: ${order.deliveredAt}, người ký: ${order.receiverSignature}`);
        break;
      case 'CANCELLED':
        console.log(`Lý do hủy: ${order.cancelReason}`);
        break;
    }
  }
  ```

---

### 2.4. Vì sao trong TypeScript hiện đại, nhiều người dùng union of literals (ví dụ `'S' | 'M' | 'L'`) thay cho `enum`?

Trả lời:
1. Zero Runtime Overhead: Union of literals hoàn toàn bị loại bỏ sau khi biên dịch sang JavaScript, không phát sinh thêm bất kỳ dòng code nào. Trong khi đó, enum thông thường sẽ sinh ra một hàm IIFE và một object phức tạp trong JavaScript (reverse mapping đối với numeric enum), làm tăng kích thước bundle.
2. Thân thiện với Tree-shaking: Do enum sinh ra mã IIFE nên các công cụ bundler (Vite, Webpack, Rollup) khó phân tích và loại bỏ dead code (tree-shake).
3. Trải nghiệm code tiện lợi & Thân thiện với API/JSON: Khi làm việc với API REST, dữ liệu trả về là chuỗi "S", "M", "L". Với Union of literals, dữ liệu tự khớp luôn mà không cần bước mapping từ string sang Enum object.
4. An toàn về kiểu: Numeric enum trong một số trường hợp cho phép gán số bất kỳ mà không báo lỗi.

---

### 2.5. Kiểu dữ liệu của TypeScript có tồn tại lúc chạy (runtime) không? Điều này khác Java thế nào và ảnh hưởng gì khi viết code?

Trả lời:
- Sự tồn tại lúc runtime: KHÔNG. Toàn bộ kiểu dữ liệu, interface, generics, type casting trong TypeScript chỉ tồn tại trong giai đoạn biên dịch (compile-time) và sẽ bị xóa sạch hoàn toàn (Type Erasure) khi chuyển sang JavaScript.
- So sánh với Java:
  - Trong Java, thông tin kiểu dữ liệu được lưu giữ trong Bytecode và được JVM quản lý lúc runtime. Java hỗ trợ cơ chế Reflection mạnh mẽ, kiểm tra kiểu qua instanceof ClassName, kiểm tra generic (ở một mức độ nhất định) và xác thực kiểu chặt chẽ tại runtime.
- Ảnh hưởng khi viết code TypeScript:
  - Không thể dùng tên interface hay type để kiểm tra lúc runtime (ví dụ if (data instanceof IProduct) sẽ gây lỗi cú pháp).
  - Không được ỷ lại TypeScript để bảo vệ dữ liệu runtime: Dữ liệu nhận từ người dùng, file hoặc API bên ngoài phải được xác thực lúc runtime bằng thư viện schema (như Zod, Yup, Valibot) hoặc viết Type Guards (arg is Type).

---

## Phần 3: Generic và utility type

### 3.1. Viết một hàm generic `first<T>(arr: T[])` trả về phần tử đầu tiên của mảng. Kiểu trả về nên là gì?

Trả lời:
- Kiểu trả về nên là T | undefined vì trong trường hợp mảng rỗng [], phần tử đầu tiên arr[0] sẽ có giá trị là undefined.
```ts
function first<T>(arr: T[]): T | undefined {
  return arr.length > 0 ? arr[0] : undefined;
}
```

---

### 3.2. Cú pháp `<T extends ...>` dùng để làm gì? Cho ví dụ.

Trả lời:
- Cú pháp <T extends ...> dùng để tạo Generic Constraint (Ràng buộc kiểu generic). Nó giới hạn kiểu dữ liệu T truyền vào bắt buộc phải thỏa mãn hoặc có chứa các thuộc tính của kiểu được quy định sau từ khóa extends.
- Ví dụ:
  ```ts
  interface HasId {
    id: number | string;
  }

  // T bắt buộc phải có ít nhất thuộc tính id
  function logEntityId<T extends HasId>(entity: T): void {
    console.log(`Entity ID: ${entity.id}`);
  }

  logEntityId({ id: 101, name: 'Bàn phím cơ' }); // Hợp lệ
  logEntityId({ name: 'Chuột không dây' });      // Lỗi biên dịch: Thiếu thuộc tính 'id'
  ```

---

### 3.3. Khi viết API cập nhật sản phẩm (chỉ gửi lên những trường cần sửa), em sẽ định nghĩa kiểu dữ liệu đầu vào thế nào? Dùng utility type nào?

Trả lời:
- Kết hợp hai utility types: Partial<T> (biến tất cả các trường thành optional) và Omit<T, K> (loại bỏ những trường không được phép sửa, như id hoặc createdAt).
- Ví dụ code:
  ```ts
  interface Product {
    id: string;
    name: string;
    price: number;
    description: string;
    createdAt: Date;
  }

  // Kiểu dữ liệu nhận vào khi update: Không cho phép sửa id và createdAt, các trường còn lại là tùy chọn
  type UpdateProductDto = Partial<Omit<Product, 'id' | 'createdAt'>>;

  // Hàm xử lý API
  function updateProduct(id: string, updateData: UpdateProductDto) {
    // updateData có thể chỉ là { price: 150000 } hoặc { name: "Tên mới" }
  }
  ```

---

## Phần 4: Bất đồng bộ

### 4.1. `async/await` hoạt động thế nào? JavaScript chạy một luồng thì làm sao xử lý được nhiều request cùng lúc?

Trả lời:
- Cách hoạt động của async/await:
  - async đảm bảo hàm luôn trả về một Promise.
  - await giúp tạm dừng quá trình thực thi của hàm async hiện tại cho đến khi Promise được giải quyết (resolve) hoặc bị từ chối (reject), sau đó unwrap lấy giá trị kết quả. Bản chất async/await là cú pháp bọc ngoài (syntactic sugar) của Promise kết hợp với Generator.
- Cách JavaScript đơn luồng xử lý nhiều request đồng thời:
  - JavaScript Engine chỉ có một luồng chính duy nhất (Call Stack) để thực thi mã JavaScript.
  - Tuy nhiên, các tác vụ I/O tốn thời gian (truy vấn Database, đọc ghi file, gọi Network request) được chuyển giao cho nền tảng đa luồng bên dưới xử lý (Web APIs trên trình duyệt hoặc thư viện C++ libuv với Thread Pool trên Node.js).
  - Nhờ cơ chế Non-blocking I/O, luồng chính của JS không cần chờ tác vụ I/O xong mà tiếp tục thực thi các đoạn code khác. Khi I/O hoàn tất, callback sẽ được đẩy vào Microtask Queue / Task Queue. Event Loop sẽ liên tục giám sát, khi nào Call Stack rỗng sẽ lấy callback từ Queue đưa lên Call Stack thực thi tiếp. Nhờ đó một luồng JS vẫn xử lý được hàng nghìn request đồng thời.

---

### 4.2. Nếu quên `await` khi gọi một hàm `async` thì chuyện gì xảy ra? Cho ví dụ.

Trả lời:
- Hậu quả khi quên await:
  1. Biến nhận giá trị sẽ là một đối tượng Promise ở trạng thái pending (Promise { <pending> }), không phải giá trị thực tế trả về.
  2. Luồng code tiếp tục chạy ngay lập tức mà không đợi tác vụ async hoàn thành, dẫn đến tình trạng Race Condition (dữ liệu phụ thuộc chưa kịp sẵn sàng).
  3. Lỗi trong hàm async không bắt được qua try...catch thông thường, dễ gây lỗi UnhandledPromiseRejection.
- Ví dụ:
  ```ts
  async function getUserBalance(userId: string): Promise<number> {
    return 500; // Giả lập lấy số dư từ DB
  }

  async function checkout() {
    const balance = getUserBalance('user_1'); // Quên await!
    // balance lúc này là Promise<number>, không phải number!
    console.log(balance + 100); 
    // Runtime JavaScript sẽ chuyển thành "[object Promise]100" hoặc báo lỗi Type trong TypeScript
  }
  ```

---

### 4.3. Xử lý lỗi trong hàm `async` như thế nào?

Trả lời:
- Cách 1: Sử dụng khối try...catch (phổ biến và chuẩn mực nhất):
  ```ts
  async function fetchData(url: string) {
    try {
      const response = await fetch(url);
      if (!response.ok) {
        throw new Error(`HTTP Error! Status: ${response.status}`);
      }
      return await response.json();
    } catch (error: unknown) {
      if (error instanceof Error) {
        console.error('Lỗi xảy ra:', error.message);
      } else {
        console.error('Lỗi không xác định:', error);
      }
      throw error; // Ném tiếp lỗi ra ngoài nếu cần xử lý ở tầng cao hơn
    }
  }
  ```
- Cách 2: Bắt lỗi trực tiếp qua .catch() trên Promise:
  ```ts
  const data = await fetchData('/api/user').catch(err => {
    console.error(err);
    return null; // fallback value
  });
  ```

---

## Phần 5: Thực hành

### 5.1. Khai báo kiểu props cho một component React `ProductList` nhận vào danh sách sản phẩm và một hàm `onSelect` được gọi khi chọn một sản phẩm.

Trả lời:
```tsx
interface Product {
  id: string;
  name: string;
  price: number;
}

interface ProductListProps {
  products: Product[];
  onSelect: (product: Product) => void;
}

export const ProductList: React.FC<ProductListProps> = ({ products, onSelect }) => {
  return (
    <ul>
      {products.map((product) => (
        <li key={product.id} onClick={() => onSelect(product)}>
          {product.name} - {product.price.toLocaleString()} VNĐ
        </li>
      ))}
    </ul>
  );
};
```

---

### 5.2. Khai báo kiểu cho body của request `POST /products` trong Express, và viết handler sử dụng kiểu đó.

Trả lời:
```ts
import { Request, Response } from 'express';

// 1. Định nghĩa kiểu dữ liệu cho Body
interface CreateProductBody {
  name: string;
  price: number;
  description?: string;
  category: string;
}

// 2. Định nghĩa kiểu cho Request của Express
// Cú pháp Request<Params, ResBody, ReqBody, ReqQuery>
type CreateProductRequest = Request<{}, {}, CreateProductBody>;

// 3. Viết handler
export const createProductHandler = async (
  req: CreateProductRequest,
  res: Response
): Promise<void> => {
  try {
    const { name, price, description, category } = req.body;

    // Validate dữ liệu cơ bản
    if (!name || price == null || price <= 0) {
      res.status(400).json({ error: 'Tên và giá hợp lệ là bắt buộc' });
      return;
    }

    // Giả lập lưu dữ liệu
    const newProduct = {
      id: Date.now().toString(),
      name,
      price,
      description,
      category,
    };

    res.status(201).json({
      message: 'Tạo sản phẩm thành công',
      data: newProduct,
    });
  } catch (error) {
    res.status(500).json({ error: 'Lỗi server nội bộ' });
  }
};
```

---

### 5.3. Đoạn code dưới đây có lỗi gì? Sửa lại cho đúng.

```ts
interface Product {
  id: number;
  name: string;
  price?: number;
}

function getTotal(products: Product[]): number {
  return products.reduce((sum, p) => sum + p.price, 0);
}

async function loadProducts(): Product[] {
  const res = await fetch('/api/products');
  return res.json();
}
```

Trả lời:
- Phân tích các lỗi:
  1. Trong hàm getTotal: Thuộc tính price trong interface Product là optional (price?: number), tức là nó có thể mang giá trị undefined. Biểu thức sum + p.price khi p.price là undefined sẽ cho kết quả là NaN lúc runtime, và TypeScript compiler (ở chế độ strictNullChecks) sẽ báo lỗi: Object is possibly 'undefined'.
  2. Trong hàm loadProducts: 
     - Hàm có từ khóa async thì giá trị trả về bắt buộc phải được bọc trong một Promise. Khai báo Product[] là sai cú pháp kiểu trả về của async function.
     - Hàm res.json() trả về Promise<any>, cần gán kiểu hoặc ép kiểu rõ ràng để đảm bảo type safety.

- Đoạn code đã sửa hoàn chỉnh:
```ts
interface Product {
  id: number;
  name: string;
  price?: number;
}

// Sửa lỗi 1: Dùng nullish coalescing (?? 0) để fallback giá trị 0 nếu price là undefined
function getTotal(products: Product[]): number {
  return products.reduce((sum, p) => sum + (p.price ?? 0), 0);
}

// Sửa lỗi 2: Khai báo kiểu trả về là Promise<Product[]> và ép kiểu dữ liệu trả về từ JSON
async function loadProducts(): Promise<Product[]> {
  const res = await fetch('/api/products');
  if (!res.ok) {
    throw new Error(`Lỗi tải sản phẩm: ${res.statusText}`);
  }
  const data: Product[] = await res.json();
  return data;
}
```
