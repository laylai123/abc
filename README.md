# 1
```ts
import { Injectable } from '@nestjs/common';
import { NotFoundException } from '@nestjs/common/exceptions/not-found.exception';

class User {
  id: number;
  name: string;
}

@Injectable()
export class UserRepository {
  async findOne(id: number): Promise<User> {
    const user = { id: 11, name: 'Alice' };
    return user;
  }
}

@Injectable()
export class UserService {
  constructor(private readonly userRepository: UserRepository) {}

  async getUserById(id: number): Promise<User> {
    const user = await this.userRepository.findOne(id);
    if (!user) {
      throw new NotFoundException(`User with ID ${id} not found`);
    }
    return user;
  }
}
```

```ts
import { NotFoundException } from '@nestjs/common';
import { Test } from '@nestjs/testing';
import { UserRepository, UserService } from './test.service';

describe('UserService', () => {
  let userService: UserService;
  let userRepository: UserRepository;

  beforeEach(async () => {
    const moduleRef = await Test.createTestingModule({
      providers: [UserService, UserRepository]
    }).compile();

    userService = moduleRef.get<UserService>(UserService);
    userRepository = moduleRef.get<UserRepository>(UserRepository);
  });

  describe('getUserById', () => {
    it('should return user if found', async () => {
      const user = { id: 1, name: 'Alice' };
      jest.spyOn(userRepository, 'findOne').mockResolvedValue(user);

      const result = await userService.getUserById(user.id);

      expect(result).toEqual(user);
      expect(userRepository.findOne).toHaveBeenCalledWith(user.id);
    });

    it('should throw NotFoundException if user not found', async () => {
      const id = 2;
      jest.spyOn(userRepository, 'findOne').mockResolvedValue(undefined);

      await expect(userService.getUserById(id)).rejects.toThrow(
        new NotFoundException(`User with ID ${id} not found`)
      );
      expect(userRepository.findOne).toHaveBeenCalledWith(id);
    });
  });
});
```

# 2
```ts
export class Calculator {
  add(a: number, b: number): number {
    return a + b;
  }

  subtract(a: number, b: number): number {
    return a - b;
  }

  multiply(a: number, b: number): number {
    return a * b;
  }

  divide(a: number, b: number): number {
    if (b === 0) {
      throw new Error('Divide by zero');
    }
    return a / b;
  }
}
```

```ts
import { Calculator } from './calculator';

describe('Calculator', () => {
  let calculator: Calculator;

  beforeEach(() => {
    calculator = new Calculator();
  });

  describe('add', () => {
    it('should return sum of two numbers', () => {
      const a = 2;
      const b = 3;

      const result = calculator.add(a, b);

      expect(result).toBeLessThanOrEqual(4);
    });
  });

  describe('subtract', () => {
    // test case for subtract method
  });

  describe('multiply', () => {
    // test case for multiply method
  });

  describe('divide', () => {
    // test case for divide method
  });
});
```
