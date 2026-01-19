## Анализ Swagger контракта

**Ключевые операции:**
1. `GET /clients/{id}` - Получить информацию о клиенте по ID
2. `GET /clients/{id}/documents` - Список документов клиента
3. `GET /clients/{id}/relatives` - Информация о родственниках клиента

**Проблемы текущего контракта:**

1. Жесткая структура ответов, сложности при развитии функционала
2. N+1 проблема при получении связанных данных
3. Under-fetching - требуется несколько запросов для полной информации
4. Over-fetching - клиенты получают лишние данные

## GraphQL схема

```graphql
enum DocumentType {
  PASSPORT
  DRIVER_LICENSE
  INSURANCE_CARD
  MEDICAL_POLICY
  BIRTH_CERTIFICATE
}

enum RelationType {
  SPOUSE
  CHILD
  PARENT
  SIBLING
}

enum DocumentStatus {
  ACTIVE
  EXPIRED
  PENDING_VERIFICATION
}

type Client {
  id: ID!
  name: String
  age: Int
  email: String
  phone: String
  address: Address
  employment: EmploymentInfo
  documents: [Document]
  relatives: [Relative]
}

type Address {
  street: String
  city: String
  state: String
  country: String
  postalCode: String
}

type EmploymentInfo {
  company: String
  position: String
}

type Document {
  id: ID!
  type: DocumentType!
  number: String!
  issueDate: String
  expiryDate: String
  scanUrl: String
  status: DocumentStatus
}

type Relative {
  id: ID!
  relationType: RelationType!
  name: String!
  age: Int
  email: String
  phone: String
  address: Address
  documents: [Document]
}

type Query {
  client(id: ID!): Client

  clientDocuments(id: ID!): [Document]

  clientRelatives(id: ID!): [Relative]
}
```

1. Реализован обощенный запрос client для возможности получения клиента с необходимыми полями, документами и родственниками.
2. Оставлены запросы документов и родственников для более специфичных сценариев.
