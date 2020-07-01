# 调用示例


### SQL检测调用示例

```golang
func TsClient(order *pb.YearningAuditOrder) {

	conn, err := grpc.Dial(address, grpc.WithInsecure())
	if err != nil {
		log.Fatalf("did not connect: %v", err)
	}
	defer conn.Close()
	c := pb.NewJunoClient(conn)

	ctx, cancel := context.WithTimeout(context.Background(), time.Second)
	defer cancel()
	r, err := c.OrderDeal(ctx, order)
	if err != nil {
		log.Fatalf("could not connect: %v", err)
	}
	for _, i := range r.Record {
		fmt.Println(i.SQL)   // SQL语句
		fmt.Println(i.Status) //审核状态
		fmt.Println(i.Level)  // 错误等级
		fmt.Println(i.Error) // 错误信息
		fmt.Println(i.AffectRows) //影响行数
	}
}

func TestTsClient(t *testing.T) {

	s := pb.YearningAuditOrder{
		SQL:      "CREATE TABLE `core_test` (`id` int unsigned AUTO_INCREMENT PRIMARY KEY COMMENT 'id',`username` varchar(50) NOT NULL COMMENT 'xxx')",
		DataBase: "test01",
		Source: &pb.Source{
			Addr:     "127.0.0.1",
			User:     "root",
			Password: "xxxxxxx",
			Port:     3306,
		},
		Check:  true,
		WorkId: "201907292801",
	}
	TsClient(&s)
}

```

### DML/DDL 语句执行并备份示例


```golang
func ExDDLClient(order *pb.YearningAuditOrder) {

	// Set up a connection to the server.
	conn, err := grpc.Dial(address, grpc.WithInsecure())
	if err != nil {
		log.Fatalf("did not connect: %v", err)
	}
	defer conn.Close()
	c := pb.NewJunoClient(conn)

	ctx, cancel := context.WithTimeout(context.Background(), time.Second)
	defer cancel()
	r, err := c.OrderDDLExec(ctx, order)
	if err != nil {
		log.Fatalf("could not connect: %v", err)
	}
	fmt.Println(r.Message)
}

func ExDMLClient(order *pb.YearningAuditOrder) {

	// Set up a connection to the server.
	conn, err := grpc.Dial(address, grpc.WithInsecure())
	if err != nil {
		log.Fatalf("did not connect: %v", err)
	}
	defer conn.Close()
	c := pb.NewJunoClient(conn)

	ctx, cancel := context.WithTimeout(context.Background(), time.Second)
	defer cancel()
	r, err := c.OrderDMLExec(ctx, order)
	if err != nil {
		log.Fatalf("could not connect: %v", err)
	}
	fmt.Println(r.Message)
}

func TestExDDLClient(t *testing.T) {

	s := pb.YearningAuditOrder{
		SQL:      "CREATE TABLE `core_grai32ned` (`id` int unsigned AUTO_INCREMENT PRIMARY KEY COMMENT 'id',`username` varchar(50) NOT NULL COMMENT 'xxx')",
		DataBase: "test01",
		Source: &pb.Source{
			Addr:     "127.0.0.1",
			User:     "root",
			Password: "xxxxxxxx",
			Port:     3306,
		},
		IsDML:   false,
		Backup:  true,
		Execute: true,
		WorkId:  "201907292801",
	}
	ExDDLClient(&s)
}

func TestExDMLClient(t *testing.T) {

	s := pb.YearningAuditOrder{
		SQL:      "INSERT INTO `Yearning`.`b_test`(`id`,`idx`,`dateux`) VALUES(66732326,'321',NULL);",
		DataBase: "test01",
		Table:    "b_test",
		Source: &pb.Source{
			Addr:     "127.0.0.1",
			User:     "root",
			Password: "xxxxxxx",
			Port:     3306,
		},
		IsDML:   true,
		Backup:  true,
		Execute: true,
		WorkId:  "201907292801",
	}
	ExDMLClient(&s)
}
```