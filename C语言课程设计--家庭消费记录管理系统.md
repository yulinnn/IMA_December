# 课内学习：C语言课程设计--家庭消费记录管理系统
## 设置链表
```C
int n = 0;//已录入消费记录的数量(链表中项的个数) 

typedef struct
{
	int date;
	char id[9];
	float amount;
	char type[9];
	char method[9];
	char site[9];
	char detail[50];       
}Consume;

typedef struct Node
{
	Consume consume;
	struct Node* next;
}Node;

Node *head,*p,*p1,*p2;
```
## 解决fputs会读取\n的问题
参考 ：《C primer plus》 s_gets函数
```C
void s_gets(char *st,int k)
{
	char *find;
	//剔除\n 
	if(fgets(st,k,stdin))
	{
		find = strchr(st,'\n');
		if(find)
		   *find = '\0';
		else
		   while(getchar() != '\n')
		   continue;   
	}
} 
```
## 剔除用户输入的空格
```C
void remove_blank(char *st) 
{
	char *find = st;
	while(*st != '\0')
	{
		if(*st != ' ')
		{
		   *find = *st;
		   find++;
		}
		st++;
	}
	*find = '\0';
}
```
## 读取函数:读取文件fee.dat
解决重点：判断fee.dat是否已创建，判断fee.dat是否为空
```C
//判断fee.dat是否存在，若不存在则创建一个fee.dat 
if((fp=fopen("fee.dat","r+"))==NULL)
{
    fp=fopen("fee.dat","w+");
    fclose(fp);
    return;
}
//判断fee.dat是否为空，若为空则放弃读取
fgetc(fp); 
if(feof(fp))
{
    head = NULL;
    return;
}
```
读取数据
```C
while(fscanf(fp,"%d %s %f %s %s %s %s",&date,id,&amount,type,method,site,detail) != EOF)
{
    p1 = (Node*)malloc(sizeof(Node));
    if(n == 0)
        head = p1;
    else
        p2->next = p1;  
    p1->consume.date = date;
    strcpy(p1->consume.id,id);
    p1->consume.amount = amount;
    strcpy(p1->consume.type,type);
    strcpy(p1->consume.method,method);
    strcpy(p1->consume.site,site);
    strcpy(p1->consume.detail,detail);
    p2 = p1;
    n++;
}
p2->next = NULL;
```
## 存储函数:将链表数据存入fee.dat
注意：写入格式与读取函数一致(用空格隔开)
## 显示已经存在的数据
作用：在用户查询数据、删除数据、修改数据时，可直接对照已有数据查询、删除或修改；在用户录入数据时，可对照已有数据，避免输入语义相似的词语(如消费品类中的“衣物”和“衣服”)

以显示消费品类为例

```C
char* show_type(void)       //获取已录入消费品类
{
	char *types[9];
	char all[100] = {0};
	int i,j;
	bool type_not_exist = true;
	for(p = head,i = 0;p != NULL;p = p->next,type_not_exist = true)
	{
	    for(j = 0;j<i;j++)
	       if(strcmp(types[j],p->consume.type) == 0)
	          type_not_exist = false;
	    if(type_not_exist)
		{
	        types[i] = p->consume.type;
			i++;	
	    }      
	}
    for(j = 0;j < i;j++)
	{
		strcat(all,types[j]);
    	strcat(all," ");
	}
	return all;
}
```
## 一、录入函数
解决重点：避免让用户输入家庭成员、消费品类、支付方式、消费场所都相同的数据（使用strcmp)

```C
//在用户录入家庭成员、消费品类、支付方式、消费场所后
//判断录入的消费记录是否重复(p1为新录入的数据) 
p = head;
bool same_exist = false;
while(n != 0 && p != p1)
{
    if(p1->consume.date == p->consume.date && strcmp(p1->consume.id,p->consume.id)==0 && strcmp(p1->consume.type,p->consume.type)==0 && strcmp(p1->consume.method,p->consume.method)==0 && strcmp(p1->consume.site,p->consume.site)==0)
    {
        printf("\n**已录入相同消费记录，请直接修改该消费记录**\n\n");
        same_exist = true;
        break;
    }
    p = p->next;
}

if(same_exist)
{ 
    p2->next = NULL;
    free(p1);
}
else
{
    //录入商品详情
    printf("请输入商品详情：");
    scanf("%s",&p1->consume.detail);
    fflush(stdin);  
    //把消费记录写入fee.dat
    fputs("\r\n",fp);
    fputs(interger,fp);
    fputs(" ",fp);
    fputs(p1->consume.id,fp);
    fputs(" ",fp);
    fputs(decimal,fp);
    fputs(" ",fp);
    fputs(p1->consume.type,fp);
    fputs(" ",fp);
    fputs(p1->consume.method,fp);
    fputs(" ",fp);
    fputs(p1->consume.site,fp);
    fputs(" ",fp);
    fputs(p1->consume.detail,fp);
    p2 = p1;
    p2->next = NULL;
    n++;
    fclose(fp);
    puts("\n**录入完成**\n");
}
```
## 二、查询函数
```C
//查询函数 
void enquiry(void);
void enq_date(void);         //按日期区间查询
void enq_amount(void);       //按交易金额查询
void enq_id_date(void);      //按家庭成员加日期区间查询
void enq_type_date(void);    //按消费品类加日期区间查询
void enq_method_date(void);  //按支付方式加日期区间查询
void enq_site_date(void);    //按消费场所加日期区间查询 
void enq_all(void);          //显示全部消费记录 
```
思路：遍历链表，用strcmp比对字符串

## 三、删除函数
```C
//删除函数
void del(void);
void del_date(void);        //按消费日期删除 
void del_date_id(void);     //按消费日期加家庭成员删除 
void del_dates(void);       //按日期区间删除
void del_dates_id(void);    //按日期区间加家庭成员删除
void del_precise(void);     //精准删除一条消费记录
```
思路：遍历链表，用strcmp比对字符串;分情况删除链表中的项(开头，中间，结尾)；删除后用存储函数存入fee.dat

精准删除：即通过逐条比对家庭成员、消费品类、支付方式和消费场所，筛选出一条消费记录并精准删除(由于在录入函数中保证了不会同时出现家庭成员、消费品类、支付方式、消费场所都相同的数据，故能够精准删除一条数据；更改函数中的方法同理)

## 四、更改函数
施工中
## 五、排序函数
施工中
## 六、统计函数
施工中
