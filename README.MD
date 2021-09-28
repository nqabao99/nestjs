# Netsjs

    Nest (NestJS) là một framework để xây dựng các ứng dụng server-side bằng Node.js hiệu quả, và dể mở rộng. Nó sử dụng ngôi ngữ bậc cao của javascrip là TypeScript (nhưng vẫn cho phép các nhà phát triển sử dụng JavaScript thuần túy) và kết hợp các tính chất của OOP (Lập trình hướng đối tượng), FP (Lập trình chức năng) và FRP (Lập trình phản ứng chức năng).

- Cài đặt:

```npm
$ npm i -g @nestjs/cli
$ nest new project-name
```

- Nên tảng:
  Nest hướng tới mục tiêu trở thành một frameworks nền tảng bất khả tri. Nền tảng độc lập giúp tạo ra các phần logic có thể tái sử dụng mà các nhà phát triển có thể tận dụng trên một số loại ứng dụng khác nhau. Về mặt kỹ thuật, Nest có thể hoạt động với bất kỳ Framework Node HTTP nào sau khi tạo bộ chuyển đổi. Có hai nền tảng HTTP được hỗ trợ ngay lập tức: express và fastify.

## Controllers

    Controllers có trách nhiệm xử lý các request gửi đến và trả lại response cho client.

    ![Controller](https://docs.nestjs.com/assets/Controllers_1.png)

    Mục đích của controller là nhận các request cụ thể cho ứng dụng. Cơ chế định tuyến (route) kiểm soát controller nào nhận được request nào. Thông thường, mỗi controller có nhiều hơn một định tuyến (route) và các định tuyến khác nhau có thể thực hiện các hành động khác nhau.

    Để tạo một controller cơ bản, chúng ta sử dụng các class và các decorator. Decorator liên kết với cácclass với metadata bắt buộc và cho phép Nest tạo ra bản đồ định tuyến (route map) (liên kết các request với controller tương ứng).

### Routing:

    Sử dụng @Controller() decorator, nó được yêu cầu để định nghĩa một controller cơ bản. Ta sẽ chỉ định tiền tố đường dẫn tuỳ chọn của cats. Sử dụng tiền tố đường dẫn trong một @Controller() decorator cho phép chúng ta dễ ràng nhóm một tập các route liên quan, giảm thiểu đoạn mã lặp.

    ```ts
    import { Controller, Get } from "@nestjs/common";

    @Controller("cats")
    export class CatsController {
        @Get()
        findAll(): string {
            return "This action returns all cats";
        }
    }
    ```

    Phương thức @Get() HTTP request decorator phía trước phương thức findAll() yêu cầu Nest tạo một handler cho một endpoint cụ thể cho các request HTTP. Endpoint tương ứng với HTTP request và đường dẫn route. Đường dẫn route cho một handler được xác định bằng cách nối tiền tố được khai báo cho controller và bất kỳ đường dẫn xác định trong một request decorator. Vì ta đã khai báo tiền tố cho mọi route (cats), và chưa thêm bất kỳ thông tin đường dẫn nào vào trong decorator, Nest sẽ map các request GET /cats đến handler này. Như đã đề cập, đường dẫn bao gồm cả tiền tố đường dẫn controller và bất kỳ chuỗi đường dẫn nào được khai báo trong phương thức request decorator.

### Request object:

    Handler thường cần truy cập vào các chi tiết request của client. Nest cung cấp quyền truy cập vào các request object của platform. Ta có thể truy cập vào request object bằng cách cung cấp Nest để inject nó bằng cách thêm @Req() decorator vào signature của handler.

    ```ts
    import { Controller, Get, Req } from '@nestjs/common';
    import { Request } from 'express';

    @Controller('cats')
    export class CatsController {
        @Get()
        findAll(@Req() request: Request): string {
            return 'This action returns all cats';
        }
    }
    ```

    Request object đại diện cho request HTTP và các thuộc tính của chuỗi query, request, parameters, HTTP headers và body.

    Trong hầu hết các trường hợp, không cần thiết phải lấy các thuộc tính theo cách thủ công. Thay vào đó, ta có thể sử dụng các decorator chuyên dụng, chẳng hạn như @Body(), @Query().

### Resources:

    Trước đó, ta đã xác định được một endpoint để tìm resource cats. Thông thường, ta cũng muốn cung cấp một endpoint để tạo các bản ghi mới. Đối với điều này, hãy tạo POST handler

    ```ts
    import { Controller, Get, Post } from '@nestjs/common';

    @Controller('cats')
    export class CatsController {
        @Post()
        create(): string {
            return 'This action adds a new cat';
        }

        @Get()
        findAll(): string {
            return 'This action returns all cats';
        }
    }
    ```

    Nest cung cấp decorator cho tất cả các phương thức HTTP tiêu chuẩn: @Get(), @Post(), @Put(), @Delete(), @Patch(), @Options(), @Head(), @All().

### Route wildcards:

    Dấu sao (*) được sử dụng làm wildcard (ký tự đại diện), và sẽ khớp với bất kỳ tổ hợp ký tự nào.

    ```ts
    @Get('ab*cd')
    findAll() {
    return 'This route uses a wildcard';
    }
    ```
    Đường dẫn route 'ab*cd' sẽ khớp với abcd, ab_cd, abecd, ... Những ký tự ?, +, *, và () có thể được sử dụng trong đường dẫn route, và là các tập con của các regular expression counterparts của chúng.

### Status code:

    Như đã đề cập, status code – response luôn mặc định là 200, ngoài trừ các POST request sẽ là 201. Ta có thể dễ dàng thay đổi hành vi này bằng thêm @HttpCode(…) decorator ở handler-level.

    ```ts
    @Post()
    @HttpCode(204)
    create() {
    return 'This action adds a new cat';
    }
    ```

### Headers

    Để chỉ định một tùy chỉnh response header, bạn có thể sử dụng một @Header() decorator hoặc response object thư viên riêng (và gọi trực tiếp res.header()).

    ```ts
    @Post()
    @Header('Cache-Control', 'none')
    create() {
    return 'This action adds a new cat';
    }
    ```

### Redirection:

    Để redirect một response tới một URL cụ thể, bạn có thể sử dụng @Redirect() decorator.

    @Redirect() nhận 2 đối số (arguments), url và statusCode, cả 2 đều là tùy chọn. Giá trị mặc định của statusCode is 302 (Found) nếu bị bỏ qua ko truyền gì.

    ```ts
    @Get()
    @Redirect('https://nestjs.com', 301)
    ```

### Route parameters:

    Các route với đường dẫn tĩnh sẽ không hoạt động khi bạn cần chấp nhận dữ liệu động như một phần của request (ví dụ: GET /cat/1 để nhận cat có id 1). Để xác định các route với các tham số, ta có thể thêm các token route parameter trong đường dẫn của route để nắm bắt giá trị động tại vị trí đó URL request. Token route parameter trong @Get() decorator ví dụ dưới thể hiện cách sử dụng này. Các tham số route được khai báo theo cách này có thể được truy cập bằng cách sử dụng @Param() decorator, cần được thêm vào method signature.

    ```ts
    @Get(':id')
    findOne(@Param() params): string {
    return `This action returns a #${params.id} cat`;
    }
    ```

### Sub-Domain Routing:

    @Controller decorator có thể có một tùy chọn host để yêu cầu máy chủ HTTP của các request đến khớp với một số giá trị cụ thể.

    ```ts
    @Controller({ host: 'jsonplaceholder.typicode.com/users' })
    export class AdminController {
    @Get()
    index(): string {
        return 'Admin page';
    }
    }
    ```

### Scopes:

    Đối với những người đến từ các nền tảng ngôn ngữ lập trình khác nhau, có thể bất ngờ khi biết rằng trong Nest, hầu hết mọi thứ đều được chia sẻ qua các request gửi đến. Chúng ta có một nhóm kết nối đến cơ sở dữ liệu, các singleton service với trạng thái toàn cục, v.v. Hãy nhớ rằng Node.js không tuân theo Mô hình không trạng thái đa luồng yêu cầu / phản hồi trong đó mọi request được xử lý bởi một luồng riêng biệt. Do đó, việc sử dụng các instance singleton hoàn toàn an toàn cho các ứng dụng của chúng tôi.

### Asynchronicity:

    Mọi hàm không đồng bộ phải trả về một Promise. Điều này có nghĩa là bạn có thể trả về giá trị hoãn lại mà Nest sẽ có thể tự resolve. Hãy xem một ví dụ về điều này:

    ```ts
    @Get()
    async findAll(): Promise<any[]> {
    return [];
    }
    ```

### Request payloads:

### Full resource sample:

    Dưới đây là một ví dụ sử dụng một số decorator có sẵn để tạo một controller cơ bản. Controller này đưa ra một số phương pháp để truy cập và thao tác dữ liệu nội bộ.

    ```ts
    import { Controller, Get, Query, Post, Body, Put, Param, Delete } from '@nestjs/common';
    import { CreateCatDto, UpdateCatDto, ListAllEntities } from './dto';

    @Controller('cats')
    export class CatsController {
    @Post()
    create(@Body() createCatDto: CreateCatDto) {
        return 'This action adds a new cat';
    }

    @Get()
    findAll(@Query() query: ListAllEntities) {
        return `This action returns all cats (limit: ${query.limit} items)`;
    }

    @Get(':id')
    findOne(@Param('id') id: string) {
        return `This action returns a #${id} cat`;
    }

    @Put(':id')
    update(@Param('id') id: string, @Body() updateCatDto: UpdateCatDto) {
        return `This action updates a #${id} cat`;
    }

    @Delete(':id')
    remove(@Param('id') id: string) {
        return `This action removes a #${id} cat`;
    }
    }
    ```

### Getting up and running:

    Với controller ở trên được xác định đầy đủ, Nest vẫn không biết rằng CatsController tồn tại và kết quả là sẽ không tạo một instance của lớp này.

    Controller luôn thuộc về một module, đó là lý do tại sao chúng tôi bao gồm mảng controllers trong @Module() decorator. Vì chúng tôi chưa xác định bất kỳ module nào khác ngoại trừ AppModule gốc, chúng tôi sẽ sử dụng module đó để giới thiệu CatsController:

    ```ts
    import { Module } from '@nestjs/common';
    import { CatsController } from './cats/cats.controller';

    @Module({
    controllers: [CatsController],
    })
    export class AppModule {}
    ```

    Chúng tôi đã đính kèm metadata vào lớp module bằng cách sử dụng @Module() decorator và Nest hiện có thể dễ dàng phản ánh controller nào cần được gắn kết.

## Providers

    Providers là một khái niệm cơ bản trong Nest. Nhiều Class Nest cơ bản có thể được coi như một provider như service, repository, factory, helpers, ... Ý tưởng chính của một provider là nó có thể inject dependencies, điều này có nghĩa là các đối tượng có thể tạo ra nhiều mối quan hệ khác nhau với nhau và chức năng nối dây các instances của đối tượng phần lớn có thể được ủy quyền cho hệ thống runtime Nest.

    ![Providers](https://docs.nestjs.com/assets/Components_1.png)

### Services:

    Service sẽ chịu trách nhiệm lưu trữ và truy xuất dữ liệu và được thiết kế để sử dụng bởi Controller, vì vậy nó là một ứng cử viên sáng giá để được xác định là một provider.

    ```ts
    import { Injectable } from '@nestjs/common';

    @Injectable()
    export class CatsService {
        findAll(): string {
        return 'This action returns all cats';
    }
    }
    ```
    @Injectable() decorator đính kèm metadata, metadata này tuyên bố rằng CatsService là một class có thể được quản lý bởi Nest IoC container.

    Chúng ta có một lớp Service để truy xuất Cats, hãy sử dụng nó bên trong Controller:

    ```ts
    import { Controller, Get } from '@nestjs/common';
    import { CatsService } from './cats.service';

    @Controller('cats')
    export class CatsController {
    constructor(private catsService: CatsService) {}

    @Get()
    async findAll() {
        return this.catsService.findAll();
    }
    }
    ```

### Dependency injection:

    Trong Nest, nhờ các khả năng của TypeScript, việc quản lý các dependencty trở nên cực kỳ dễ dàng vì chúng chỉ được giải quyết theo kiểu. Trong ví dụ dưới, Nest sẽ giải quyết các catsService bằng cách tạo và trả về 1 instance của CatsService. Dependency này được resolved và truyền tới constructor của controller.

    ```ts
    constructor(private catsService: CatsService) {}
    ```

### Scopes:

    Các Providers thường có vòng đời (“phạm vi”) được đồng bộ hóa với vòng đời ứng dụng. Khi ứng dụng được khởi động, mọi dependency phải được resolved và do đó mọi Provider phải được khởi tạo. Tương tự, khi ứng dụng tắt, mỗi Provider sẽ bị hủy. Tuy nhiên, có nhiều cách để làm cho phạm vi vòng đợi request của Provider của bạn là tốt.

### Optional providers:

### Property-based injection:

### Provider registration:

    Bây giờ chúng tôi đã xác định một Provider (CatsService) và chúng tôi có một client sử dụng Service đó (CatsController), chúng tôi cần đăng ký Service với Nest để nó có thể thực hiện việc inject. Chúng tôi thực hiện việc này bằng cách chỉnh sửa tệp module của mình (app.module.ts) và thêm Service vào mảng providers của @Module() decorator.

    ```ts
    app.module.tsJS

    import { Module } from '@nestjs/common';
    import { CatsController } from './cats/cats.controller';
    import { CatsService } from './cats/cats.service';

    @Module({
    controllers: [CatsController],
    providers: [CatsService],
    })
    export class AppModule {}
    ```

## Module

    Module là một class được chú thích bằng @Module() decorator. @Module() decorator cung cấp metadata mà Nest sử dụng để tổ chức cấu trúc ứng dụng.

    ![Module](https://docs.nestjs.com/assets/Modules_1.png)

    Mỗi ứng dụng có ít nhất một module, một module gốc. Module gốc là điểm khởi đầu Nest sử dụng để xây dựng biểu đồ ứng dụng – cấu trúc dữ liệu nội bộ mà Nest sử dụng để giải quyết các mối quan hệ và phụ thuộc của Module và các Providers.

### Feature modules:

    CatsController và CatsService thuộc cùng một miền ứng dụng. Vì chúng có liên quan chặt chẽ với nhau, nên chuyển chúng vào một module tính năng sẽ rất hợp lý. Module tính năng chỉ đơn giản là tổ chức mã phù hợp với một tính năng cụ thể, giữ cho mã có tổ chức và thiết lập ranh giới rõ ràng. Điều này giúp chúng tôi quản lý sự phức tạp và phát triển với các nguyên tắc SOLID, đặc biệt là khi quy mô của ứng dụng và / hoặc nhóm ngày càng tăng.

    ```ts
    import { Module } from '@nestjs/common';
    import { CatsController } from './cats.controller';
    import { CatsService } from './cats.service';

    @Module({
    controllers: [CatsController],
    providers: [CatsService],
    })
    export class CatsModule {}
    ```

    Điều cuối cùng là import module này vào mô-đun gốc (AppModule, được định nghĩa trong tệp app.module.ts).

    ```ts
    import { Module } from '@nestjs/common';
    import { CatsModule } from './cats/cats.module';

    @Module({
    imports: [CatsModule],
    })
    export class AppModule {}
    ```

### Shared modules:

    Trong Nest, các module là singleton theo mặc định, và do đó bạn có thể chia sẻ cùng một thực thể của bất kỳ provider nào giữa nhiều module một cách dễ dàng.

    ![SharedModules](https://i2.wp.com/docs.nestjs.com/assets/Shared_Module_1.png?resize=640%2C246&ssl=1)

    Mỗi module tự động là một module được chia sẻ. Sau khi được tạo, nó có thể được sử dụng lại bởi bất kỳ module nào. Hãy tưởng tượng rằng chúng ta muốn chia sẻ một thực thể của CatsService giữa một số module khác. Để làm điều đó, trước tiên chúng ta cần export provider CatsService bằng cách thêm nó vào mảng exports của module, như được hiển thị bên dưới:

    ```ts
    import { Module } from '@nestjs/common';
    import { CatsController } from './cats.controller';
    import { CatsService } from './cats.service';

    @Module({
    controllers: [CatsController],
    providers: [CatsService],
    exports: [CatsService]
    })
    export class CatsModule {}
    ```

    Bây giờ bất kỳ module nào import CatsModule đều có quyền truy cập vào CatsService và sẽ chia sẻ cùng một instance với tất cả các module khác cũng import nó.

### Module re-exporting:

    Như đã thấy ở trên, Module có thể export các providers nội bộ của chúng. Ngoài ra, họ có thể re-import các module mà họ đã import. Trong ví dụ bên dưới, CommonModule được imported và export từ CoreModule, làm cho nó có sẵn cho các module khác nhập module này.

    ```ts
    @Module({
    imports: [CommonModule],
    exports: [CommonModule],
    })
    export class CoreModule {}
    ```

### Global modules:

    Khi muốn cung cấp một tập hợp các providers nên có sẵn ở mọi nơi ngay lập tức, hãy đặt module toàn cục với @Global() decorator.

    ```ts
    import { Module, Global } from '@nestjs/common';
    import { CatsController } from './cats.controller';
    import { CatsService } from './cats.service';

    @Global()
    @Module({
    controllers: [CatsController],
    providers: [CatsService],
    exports: [CatsService],
    })
    export class CatsModule {}
    ```

    @Global() làm cho module có phạm vi toàn cục. Module toàn cục chỉ nên được đăng ký một lần, thường là module gốc hoặc module lõi. Trong ví dụ trên, provider CatsService sẽ phổ biến và các module muốn đưa service vào sẽ không cần nhập CatsModule trong mảng imports của chúng.

### Dynamic modules:

## Middleware

    Middleware là một hàm được gọi trước khi tới handler route. Các hàm middleware có quyền truy cập vào các object request và response cũng như hàm middleware next() trong chu trình request-response của ứng dụng. Hàm middlware next thường được ký hiệu bằng một biến có tên là next.

    ![Middleware](https://docs.nestjs.com/assets/Middlewares_1.png)

    Các hàm Middleware có thể thực hiện các nhiệm vụ sau:

        + Thực thi bất kỳ mã nào.
        + Thực hiện các thay đổi đối với request và response object.
        + Kết thúc chu kỳ request-response.
        + Gọi hàm middleware tiếp theo trong ngăn xếp.
        + Nếu hàm middleware hiện tại không kết thúc chu kỳ request-response, nó phải gọi next() để chuyển quyền điều khiển cho hàm middleware tiếp theo. Nếu không, request sẽ bị treo.

    Bạn triển khai middleware Nest tùy chỉnh trong một hàm hoặc trong một class với @Injectable() decorator. Lớp nên implement interface NestMiddleware, trong khi hàm này không có bất kỳ yêu cầu đặc biệt nào. Hãy bắt đầu bằng cách triển khai một tính năng middleware đơn giản bằng cách sử dụng phương thức lớp.

    ```ts
    import { Injectable, NestMiddleware } from '@nestjs/common';
    import { Request, Response, NextFunction } from 'express';

    @Injectable()
    export class LoggerMiddleware implements NestMiddleware {
    use(req: Request, res: Response, next: NextFunction) {
        console.log('Request...');
        next();
    }
    }
    ```

### Dependency injection:

    Middleware Nest hỗ trợ đầy đủ Dependency Injection. Cũng như với các provider và controller, họ có thể inject dependencies có sẵn trong cùng một mô-đun. Như thường lệ, điều này được thực hiện thông qua construstor.

### Applying middleware:

    Không có chỗ cho middleware trong @Module() decorator. Thay vào đó, chúng tôi thiết lập chúng bằng phương thức configure() của lớp mô-đun. Các mô-đun bao gồm middleware phải triển khai interface NestModule. Hãy thiết lập LoggerMiddleware ở cấp AppModule.

    ```ts
    import { Module, NestModule, MiddlewareConsumer } from '@nestjs/common';
    import { LoggerMiddleware } from './common/middleware/logger.middleware';
    import { CatsModule } from './cats/cats.module';

    @Module({
    imports: [CatsModule],
    })
    export class AppModule implements NestModule {
    configure(consumer: MiddlewareConsumer) {
        consumer
        .apply(LoggerMiddleware)
        .forRoutes('cats');
    }
    }
    ```

    Trong ví dụ trên, chúng ta đã thiết lập LoggerMiddleware cho các route handler /cats đã được xác định trước đó bên trong CatsController. Chúng tôi cũng có thể hạn chế thêm middleware đối với một phương thức yêu cầu cụ thể bằng cách chuyển một đối tượng chứa đường dẫn route và phương thức request đến phương thức forRoutes() khi định cấu hình middleware. Trong ví dụ dưới đây, lưu ý rằng chúng tôi nhập enum RequestMethod để tham chiếu kiểu phương thức request mong muốn.

    ```ts
    import { Module, NestModule, RequestMethod, MiddlewareConsumer } from '@nestjs/common';
    import { LoggerMiddleware } from './common/middleware/logger.middleware';
    import { CatsModule } from './cats/cats.module';

    @Module({
    imports: [CatsModule],
    })
    export class AppModule implements NestModule {
    configure(consumer: MiddlewareConsumer) {
        consumer
        .apply(LoggerMiddleware)
        .forRoutes({ path: 'cats', method: RequestMethod.GET });
    }
    }
    ```

### Route wildcards:

    Pattern dựa trên route cũng được hỗ trơ. Ví dụ, dấu sao (*) được sử dụng làm ký tự đại diện và sẽ khớp với bất kỳ tổ hợp ký tự nào:

    ```ts
    forRoutes({ path: 'ab*cd', method: RequestMethod.ALL });
    ```

    Đường dẫn route 'ab*cd' sẽ khớp với abcd, ab_cd, abecd và vân vân. Những ký tự ?, +, *, và () có thể được sử dụng trong đường dẫn route, và là các tập con của các regular expression counterparts của chúng.

### Middleware consumer:

    MiddlewareConsumer là một class helper. Nó cung cấp một số phương pháp tích hợp middleware. Tất cả chúng có thể được xâu chuỗi 1 cách đơn giản trôi chảy. Phương thức forRoutes() có thể nhận được 1 chuỗi đơn, nhiều chuỗi, 1 đối tượng RouteInfo, 1 lớp controller và thậm chí nhiều lớp controller.Trong hầu hết các trường hợp, bạn có thể sẽ chỉ chuyển 1 danh sách các controller được phân tách bằng dấu phẩy. Dưới đây là 1 ví dụ với 1 controller duy nhất.

    ```ts
    import { Module, NestModule, MiddlewareConsumer } from '@nestjs/common';
    import { LoggerMiddleware } from './common/middleware/logger.middleware';
    import { CatsModule } from './cats/cats.module';
    import { CatsController } from './cats/cats.controller.ts';

    @Module({
    imports: [CatsModule],
    })
    export class AppModule implements NestModule {
    configure(consumer: MiddlewareConsumer) {
        consumer
        .apply(LoggerMiddleware)
        .forRoutes(CatsController);
    }
    }
    ```

### Excluding routes:

    Đôi khi chúng tôi muốn loại trừ một số route nhất định khỏi việc áp dụng middleware. Chúng ta có thể dễ dàng loại trừ các route nhất định bằng phương thức exclude(). Phương pháp này có thể lấy một chuỗi đơn, nhiều chuỗi hoặc một đối tượng RouteInfo xác định các route bị loại trừ, như được hiển thị bên dưới:

    ```ts
    consumer
    .apply(LoggerMiddleware)
    .exclude(
        { path: 'cats', method: RequestMethod.GET },
        { path: 'cats', method: RequestMethod.POST },
        'cats/(.*)',
    )
    .forRoutes(CatsController);
    ```

### Functional middleware:

### Multiple middleware:

    Như đã đề cập ở trên, để liên kết nhiều middleware được thực thi tuần tự, chỉ cần cung cấp một danh sách được phân tách bằng dấu phẩy bên trong phương thức apply():

    ```ts
    consumer.apply(cors(), helmet(), logger).forRoutes(CatsController);
    ```

### Global middleware:

    Nếu chúng tôi muốn liên kết middleware với mọi route đã đăng ký cùng một lúc, chúng tôi có thể sử dụng phương thức use() được cung cấp bởi thực thể INestApplication:

    ```ts
    const app = await NestFactory.create(AppModule);
    app.use(logger);
    await app.listen(3000);
    ```

## Exception filters

    Nest đi kèm với một lớp exception tích hợp, lớp này chịu trách nhiệm xử lý tất cả các trường hợp exception chưa được xử lý trên một ứng dụng. Khi một exception không được mã ứng dụng của bạn xử lý, nó sẽ bị lớp này bắt giữ, lớp này sau đó sẽ tự động gửi một phản hồi thân thiện với người dùng thích hợp.

    ![Exceptionfilters](https://docs.nestjs.com/assets/Filter_1.png)

    Ngoài ra, hành động này được thực hiện bởi một bộ lọc exception chung tích hợp sẵn, bộ lọc này xử lý các exception của kiểu HttpException (và các lớp con của nó). Khi một ngoại lệ không được công nhận (không phải là HttpException cũng không phải là một lớp kế thừa từ HttpException), bộ lọc exception tích hợp sẽ tạo ra phản hồi JSON mặc định sau:

    ```json
    {
    "statusCode": 500,
    "message": "Internal server error"
    }
    ```

### Throwing standard exceptions

    Nest cung cấp lớp HttpException tích hợp sẵn, được hiển thị từ gói @nestjs/common. Đối với các ứng dụng dựa trên API HTTP REST / GraphQL điển hình, cách tốt nhất là gửi các đối tượng response HTTP tiêu chuẩn khi các điều kiện lỗi nhất định xảy ra.

    Hàm tạo HttpException nhận hai đối số bắt buộc để xác định phản hồi:

        + Đối số response xác định body response JSON. Nó có thể là 1 string hoặc 1 object như mô ta bên dưới.
        + Đối số status được định nghĩa như ở HTTP status code.

    Theo mặc định, body response JSON chứa hai thuộc tính:

        + statusCode: mặc định thành HTTP status code được cung cấp ở đối số status
        + message: một đoạn mô ta ngắn về lỗi HTTP dựa trên status

    Để chỉ ghi đè phần thông báo của body response JSON, hãy cung cấp một string trong đối số response. Để ghi đè toàn bộ body response JSON, hãy chuyển một đối tượng vào đối số response. Nest sẽ chuyển hóa đối tượng và trả về nó dưới dạng body response JSON.

    Đối số phương thức khởi tạo thứ hai – status – phải là HTTP status code hợp lệ. Cách tốt nhất là sử dụng enum HttpStatus được nhập từ @nestjs/common.

    ```ts
    @Get()
    async findAll() {
        throw new HttpException({
            status: HttpStatus.FORBIDDEN,
            error: 'This is a custom message',
        }, HttpStatus.FORBIDDEN);
    }
    ```

    Response sẽ trông như này:

    ```json
    {
    "status": 403,
    "error": "This is a custom message"
    }
    ```

### Custom exceptions:

    Trong nhiều trường hợp, bạn sẽ không cần phải viết các exception tùy chỉnh và có thể sử dụng exception Nest HTTP tích hợp sẵn, như được mô tả trong phần tiếp theo. Nếu bạn cần tạo các exception tùy chỉnh, bạn nên tạo hệ thống phân cấp exception của riêng mình, nơi các exception tùy chỉnh của bạn kế thừa từ lớp HttpException cơ sở. Với cách tiếp cận này, Nest sẽ nhận ra các trường hợp exception của bạn và tự động xử lý các response lỗi. Hãy triển khai một exception tùy chỉnh như vậy:

    ```ts
    export class ForbiddenException extends HttpException {
    constructor() {
        super('Forbidden', HttpStatus.FORBIDDEN);
        }
    }
    ```

    Vì ForbiddenException mở rộng HttpException cơ sở, nó sẽ hoạt động liền mạch với exception handler được tích hợp sẵn và do đó chúng ta có thể sử dụng nó bên trong phương thức findAll().

    ```ts
    @Get()
    async findAll() {
        throw new ForbiddenException();
    }
    ```

### Built-in HTTP exceptions:

    Nest cung cấp một tập hợp các exception tiêu chuẩn kế thừa từ HttpException cơ sở. Chúng được hiển thị từ gói @nestjs/common và đại diện cho nhiều exception HTTP phổ biến nhất:

        + BadRequestException
        + UnauthorizedException
        + NotFoundException
        + ForbiddenException
        + NotAcceptableException
        + RequestTimeoutException
        + ConflictException
        + GoneException
        + HttpVersionNotSupportedException
        + PayloadTooLargeException
        + UnsupportedMediaTypeException
        + UnprocessableEntityException
        + InternalServerErrorException
        + NotImplementedException
        + ImATeapotException
        + MethodNotAllowedException
        + BadGatewayException
        + ServiceUnavailableException
        + GatewayTimeoutException
        + PreconditionFailedException

### Exception filters:

    Mặc dù bộ lọc exception cơ sở (tích hợp sẵn) có thể tự động xử lý nhiều trường hợp cho bạn, bạn có thể muốn toàn quyền kiểm soát lớp exception.

    Hãy tạo một bộ lọc exception chịu trách nhiệm bắt các exception là một instance của lớp HttpException và triển khai logic response tùy chỉnh cho chúng. Để làm điều này, chúng tôi sẽ cần truy cập vào các đối tượng Request và Respose của nền tảng cơ bản. Chúng tôi sẽ truy cập đối tượng Request để có thể lấy ra url ban đầu và đưa url đó vào thông tin ghi nhật ký. Chúng tôi sẽ sử dụng đối tượng Response để kiểm soát trực tiếp response được gửi bằng cách sử dụng phương thức response.json().

    ```js
    import { ExceptionFilter, Catch, ArgumentsHost, HttpException } from '@nestjs/common';
    import { Request, Response } from 'express';

    @Catch(HttpException)
    export class HttpExceptionFilter implements ExceptionFilter {
        catch(exception: HttpException, host: ArgumentsHost) {
            const ctx = host.switchToHttp();
            const response = ctx.getResponse<Response>();
            const request = ctx.getRequest<Request>();
            const status = exception.getStatus();

            response
            .status(status)
            .json({
                statusCode: status,
                timestamp: new Date().toISOString(),
                path: request.url,
            });
        }
    }
    ```

    @Catch(HttpException) decorator liên kết metadata bắt buộc với bộ lọc exception, cho Nest biết rằng bộ lọc cụ thể này đang tìm kiếm các exception của loại HttpException và không có gì khác. @Catch() decorator có thể nhận một tham số duy nhất hoặc một danh sách được phân tách bằng dấu phẩy. Điều này cho phép bạn thiết lập bộ lọc cho một số loại exception cùng một lúc.

### Arguments host:

    Hãy xem các tham số của phương thức catch(). Tham số exception là đối tượng exception hiện đang được xử lý. Tham số máy chủ lưu trữ là một đối tượng ArgumentsHost. ArgumentsHost là một đối tượng tiện ích mạnh mẽ mà chúng ta sẽ xem xét kỹ hơn trong chương ngữ cảnh thực thi. Trong mẫu mã này, chúng tôi sử dụng nó để lấy tham chiếu đến các đối tượng Request và Response đang được chuyển đến response handler ban đầu (trong controller nơi bắt nguồn exception). Trong mẫu mã này, chúng tôi đã sử dụng một số phương thức trợ giúp trên ArgumentsHost để có được các đối tượng Request và Response mong muốn. Tìm hiểu thêm về ArgumentsHost tại đây.

### Catch everything:

    Để bắt mọi exception chưa được xử lý (bất kể loại exception), hãy để trống danh sách tham số của @Catch() decorator, ví dụ: @Catch().

    ```ts
    import {
    ExceptionFilter,
    Catch,
    ArgumentsHost,
    HttpException,
    HttpStatus,
    } from '@nestjs/common';

    @Catch()
    export class AllExceptionsFilter implements ExceptionFilter {
        catch(exception: unknown, host: ArgumentsHost) {
            const ctx = host.switchToHttp();
            const response = ctx.getResponse();
            const request = ctx.getRequest();

            const status =
            exception instanceof HttpException
                ? exception.getStatus()
                : HttpStatus.INTERNAL_SERVER_ERROR;

            response.status(status).json({
            statusCode: status,
            timestamp: new Date().toISOString(),
            path: request.url,
            });
        }
    }
    ```

    Trong ví dụ trên, bộ lọc sẽ bắt từng exception được ném ra, bất kể loại (lớp) của nó.

### Inheritance:

    Thông thường, bạn sẽ tạo các bộ lọc exception hoàn toàn tùy chỉnh được tạo thủ công để đáp ứng các yêu cầu ứng dụng của bạn. Tuy nhiên, có thể có các trường hợp sử dụng khi bạn chỉ muốn mở rộng bộ lọc exception chung mặc định được tích hợp sẵn và ghi đè hành vi dựa trên các yếu tố nhất định.

    Để ủy quyền xử lý exception cho bộ lọc cơ sở, bạn cần mở rộng BaseExceptionFilter và gọi phương thức catch() được kế thừa.

    ```ts
    import { Catch, ArgumentsHost } from '@nestjs/common';
    import { BaseExceptionFilter } from '@nestjs/core';

    @Catch()
    export class AllExceptionsFilter extends BaseExceptionFilter {
        catch(exception: unknown, host: ArgumentsHost) {
            super.catch(exception, host);
        }
    }
    ```

## Pipe

    Một Pipe là một lớp được chú thích bằng @Injectable(). Pipes nên triển khai interface PipeTransform.

    ![Pipe](https://docs.nestjs.com/assets/Pipe_1.png)

    Pipes có hai trường hợp sử dụng điển hình:

        + Transformation: chuyển đổi dữ liệu đầu vào sang dạng mong muốn (ví dụ: từ chuỗi thành số nguyên)
        + Validation: đánh giá dữ liệu đầu vào và nếu hợp lệ, chỉ cần chuyển nó qua không thay đổi; nếu không, hãy ném một exception khi dữ liệu không chính xác

### Built-in pipes:

    Nest đi kèm với 6 pipes có sẵn dùng được ngay:

        + ValidationPipe
        + ParseIntPipe
        + ParseBoolPipe
        + ParseArrayPipe
        + ParseUUIDPipe
        + DefaultValuePipe

### Binding pipes:

    Để sử dụng một pipe, chúng ta cần liên kết một instance của lớp pipe với ngữ cảnh thích hợp. Trong ví dụ ParseIntPipe của chúng tôi, chúng tôi muốn liên kết pipe với một phương thức route handler cụ thể và đảm bảo rằng nó chạy trước khi phương thức được gọi. Chúng tôi làm như vậy với cấu trúc sau, mà chúng tôi sẽ gọi là ràng buộc pipe ở cấp tham số phương thức:

    ```ts
    @Get(':id')
    async findOne(@Param('id', ParseIntPipe) id: number) {
        return this.catsService.findOne(id);
    }
    ```

    Giả sử route được gọi như sau:

    ```
    GET localhost:3000/abc
    ```

    Nest sẽ đưa ra một exception như thế này:

    ```json
    {
    "statusCode": 400,
    "message": "Validation failed (numeric string is expected)",
    "error": "Bad Request"
    }
    ```

    Exception sẽ ngăn body của phương thức findOne() thực thi.

### Custom pipes:

    Như đã đề cập, bạn có thể xây dựng các pipes tùy chỉnh của riêng mình. Mặc dù Nest cung cấp ParseIntPipe và ValidationPipe tích hợp mạnh mẽ, chúng ta hãy xây dựng các phiên bản tùy chỉnh đơn giản của từng loại từ đầu để xem cách các pipes tùy chỉnh được xây dựng.

    ```ts
    import { PipeTransform, Injectable, ArgumentMetadata } from '@nestjs/common';

    @Injectable()
    export class ValidationPipe implements PipeTransform {
        transform(value: any, metadata: ArgumentMetadata) {
            return value;
        }
    }
    ```

    Mọi pipes implement phải có phương thức transform() để đúng với việc đã implement interface PipeTransform. Phương thức này có hai tham số:

        + value
        + metadata

    Tham số value là đối số phương thức hiện đang được xử lý (trước khi nó được phương thức route handler nhận) và metadata là metadata của đối số phương thức hiện được xử lý.

### Schema based validation:

    Hãy làm cho pipe validation của chúng tôi hữu ích hơn một chút. Hãy xem xét kỹ hơn phương thức create() của CatsController, nơi chúng tôi có thể muốn đảm bảo rằng đối tượng post body là hợp lệ trước khi cố gắng chạy phương thức service của chúng tôi.

    ```ts
    @Post()
    async create(@Body() createCatDto: CreateCatDto) {
        this.catsService.create(createCatDto);
    }
    ```

    Hãy tập trung vào tham số body createCatDto. Loại của nó là CreateCatDto:

    ```ts
    export class CreateCatDto {
        name: string;
        age: number;
        breed: string;
    }
    ```

    Chúng tôi muốn đảm bảo rằng bất kỳ request nào đến phương thức tạo (createCatDto) đều chứa một body hợp lệ. Vì vậy, chúng ta phải validate ba thành phần của đối tượng createCatDto. Chúng ta có thể làm điều này bên trong phương thức route handler, nhưng làm như vậy không phải là lý tưởng vì nó sẽ phá vỡ Nguyên tắc một trách nhiệm (SRP).

### Object schema validation:

    Thư viện Joi cho phép bạn tạo các lược đồ một cách đơn giản, với một API có thể đọc được. Hãy xây dựng một pipe valiodation sử dụng các lược đồ dựa trên Joi.

    Bắt đầu bằng cách cài đặt gói yêu cầu:

    ```
    $ npm install --save joi
    $ npm install --save-dev @types/joi
    ```

    Trong mẫu mã bên dưới, chúng tôi tạo một lớp đơn giản lấy một lược đồ làm đối số phương thức khởi tạo. Sau đó, chúng tôi áp dụng phương thức schema.validate(), phương thức này xác thực đối số đến của chúng tôi so với lược đồ đã cung cấp.

    Như đã lưu ý trước đó, một pipe validation trả về giá trị không thay đổi hoặc ném một exception.

    Trong phần tiếp theo, bạn sẽ thấy cách chúng tôi cung cấp lược đồ thích hợp cho một phương thức controller nhất định bằng cách sử dụng @UsePipes() decorator. Làm như vậy làm cho pipe validation của chúng tôi có thể sử dụng lại trên các ngữ cảnh, giống như chúng tôi đã đặt ra.

    ```ts
    import { PipeTransform, Injectable, ArgumentMetadata, BadRequestException } from '@nestjs/common';
    import { ObjectSchema } from 'joi';

    @Injectable()
    export class JoiValidationPipe implements PipeTransform {
    constructor(private schema: ObjectSchema) {}

    transform(value: any, metadata: ArgumentMetadata) {
        const { error } = this.schema.validate(value);
        if (error) {
        throw new BadRequestException('Validation failed');
        }
        return value;
    }
    }
    ```

### Binding validation pipes
