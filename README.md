# composite-key-prima-orm (resolve infinite incremental id)

```
model UsersDistributors {
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  active    Boolean  @default(false)

  //foreign key
  userId Int
  user   User @relation(fields: [userId], references: [id], onUpdate: NoAction)

  //foreign key
  distributorId Int
  distributor   Distributor @relation(fields: [distributorId], references: [id], onUpdate: NoAction)

  @@id([userId, distributorId])
  @@map("users_distributors")
}
```


```
  async setDistributor(userId: number, data: SetUserDistributor) {
    const createOrUpdateData = { userId, ...data };
    const distributors = await this.prisma.usersDistributors.upsert({
      where: {
        userId_distributorId: { distributorId: data.distributorId, userId },
      },
      create: createOrUpdateData,
      update: createOrUpdateData,
    });

    await this.update(userId, { isNew: false });

    return distributors;
  }
```
