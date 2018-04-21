---
title: "[Android] RecyclerView Example"
categories:
  - Android
tags:
  - Android_widget
---
안드로이드 v7의 RecyclerView 사용법에 대해 알아보자.

## 1. 내용

리스트 아이템 하나를 코딩한 뒤, 이 아이템을 계속 반복하여 사용(Recycle)하며 리스트를 보인다.

![example_view]({{ site.url }}{{ site.baseurl }}/img/android/rcv1.jpg)

&nbsp;

## 2. 코드

- RecyclerView를 관리할 Adapter 클래스를 새로 생성한다.

```java
public class RcvAdapter extends RecyclerView.Adapter<RcvAdapter.ViewHolder> {

    private Context mContext;
    private ArrayList<DataForm> dataList;
    // 예시에서는 DataForm Class에 Data가 담겨있음을 전제로 한다. DataForm을 원하는 클래스로 바꿔주자.

    public RcvAdapter(Context mContext, ArrayList<DataForm> dataList) {
        this.mContext = mContext;
        this.dataList = dataList;
    }

    @Override
    public int getItemCount() {
        return dataList.size();
    }

    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item, parent, false);
        ViewHolder viewHolder = new ViewHolder(view);
        return viewHolder;
    }

    class ViewHolder extends RecyclerView.ViewHolder {

        public ViewHolder(View itemView) {
            super(itemView);

        }
    }

    @Override
    public void onBindViewHolder(final ViewHolder holder, final int position) {

    }

    private void removeItem(int position) {
        dataList.remove(position);
        notifyItemRemoved(position);
        notifyItemRangeChanged(position, dataList.size()); 
    }
}
```

- RecyclerView를 사용할 Activity 클래스에서 수정한다.

```java
public class MainActivity extends AppCompatActivity {

    RecyclerView rcv;
    RcvAdapter rcvAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        rcv = (RecyclerView) findViewById(R.id.main_rcv);
        rcv.setLayoutManager(new LinearLayoutManager(this));

        rcvAdapter = new RcvAdapter(this, list);
        rcv.setAdapter(rcvAdapter);
    }
}
```

- List에 넣을 item을 xml 파일로 생성한다. 아래는 예제에서 쓴 예시 item.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?android:attr/selectableItemBackground"
    android:minHeight="56dp"
    android:orientation="horizontal">

    <ImageView
        android:id="@+id/item_iv_icon"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:layout_gravity="center_vertical"
        android:layout_marginStart="8dp"
        app:srcCompat="@drawable/ic_number_1" />

    <TextView
        android:id="@+id/item_tv_name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:layout_marginEnd="16dp"
        android:layout_marginStart="8dp"
        android:text="data.name"
        android:textColor="@android:color/black"
        android:textSize="16sp" />
</LinearLayout>
```

- 예제의 DataForm 클래스

```java
public class DataForm {
    private String name;
    private int imageNumber;

    public DataForm(String name, int imageNumber) {
        this.name = name;
        this.imageNumber = imageNumber;
    }

    public String getName() {
        return name;
    }

    public int getImageNumber() {
        return imageNumber;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setImageNumber(int imageNumber) {
        this.imageNumber = imageNumber;
    }
}
```

&nbsp;

## 3. 코드 설명

- int getItemCount()

    RecyclerView에 item이 몇 개 있는지 설정한다.

    예제에서는 dataList의 size.

&nbsp;

- ViewHolder onCreateViewHolder

    ViewHolder를 생성한다.

    ```java
    View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item, parent, false);
    ```

    위의 코드에서 R.layout.item 부분에 원하는 xml파일의 주소를 넣어주면 해당 xml 파일이 ViewHolder가 된다.

&nbsp;

- class ViewHolder extends RecyclerView.ViewHolder

    ViewHolder를 설정하는 객체 class.

    아래 예시 코드의 주석을 참고.

    ```java
    class ViewHolder extends RecyclerView.ViewHolder {
        
        // ViewHolder 클래스 내에서 View들을 자유롭게 사용하기 위해 이 위치에 View들을 선언한다.
        ImageView ivIcon;
        TextView tvName;

        public ViewHolder(View itemView) {
            super(itemView);
            
            // ViewHolder의 생성자.
            // ViewHolder의 View들을 세팅해주고, onClickListener들을 설정해준다.

            ivIcon = (ImageView) itemView.findViewById(R.id.item_iv_icon);
            tvName = (TextView) itemView.findViewById(R.id.item_tv_name);

            itemView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    Toast.makeText(activity, "click " + dataList.get(getAdapterPosition()).getName(), Toast.LENGTH_SHORT).show();
                }
            });

            itemView.setOnLongClickListener(new View.OnLongClickListener() {
                @Override
                public boolean onLongClick(View view) {
                    Toast.makeText(activity, "remove " + dataList.get(getAdapterPosition()).getName(), Toast.LENGTH_SHORT).show();
                    removeItem(getAdapterPosition());
                    return false;
                }
            });
        }
    }
    ```

- void onBindViewHolder
    
    각각의 리스트 아이템에 데이터를 입력(Bind)한다.

    ```java
    @Override
    public void onBindViewHolder(final ViewHolder holder, final int position) {
        DataForm tmpData = dataList.get(position); // dataList에서 현재 position의 data를 받아온다.

        holder.tvName.setText(tmpData.getName());
        switch(tmpData.getImageNumber()){
            case 1:
                holder.ivIcon.setImageResource(R.drawable.ic_number_1);
                break;
            case 2:
                holder.ivIcon.setImageResource(R.drawable.ic_number_2);
                break;
            case 3:
                holder.ivIcon.setImageResource(R.drawable.ic_number_3);
                break;
            case 4:
                holder.ivIcon.setImageResource(R.drawable.ic_number_4);
                break;
        }
    }
    ```

- void removeItem

    Item을 삭제할 때 호출할 함수.

    ```java
    private void removeItem(int position) {
        dataList.remove(position); // dataList에서 삭제할 position의 아이템을 삭제한다.
        
        notifyItemRemoved(position); // RecyclerView에 해당 position의 아이템이 삭제되었음을 알린다. 

        notifyItemRangeChanged(position, dataList.size()); // 삭제된 position부터 마지막 position까지의 아이템들을 새로고침한다.
        // (중간의 아이템이 삭제될 경우 모든 아이템의 position이 1씩 줄어들기 때문.)
    }
    ```

- Activity 내에서 선언

    ```java
    rcv = (RecyclerView) findViewById(R.id.main_rcv); // 코드 내의 RecyclerView 객체와 xml상의 RecyclerView를 연결한다. 
    
    rcv.setLayoutManager(new LinearLayoutManager(this)); // LayoutManager를 설정해 준다.

    rcvAdapter = new RcvAdapter(this, list); // Adapter 객체를 생성한다.
    
    rcv.setAdapter(rcvAdapter); // rcv에 adapter를 설정한다.
    ```

&nbsp;

## 4. 더 알아보기

### 4.1. 구분선을 넣고 싶을 경우

Activity 클래스에서 LinearLayoutManager를 이렇게 설정해준다.

```java
LinearLayoutManager linearLayoutManager = new LinearLayoutManager(this);
rcv.addItemDecoration(new DividerItemDecoration(this, linearLayoutManager.getOrientation()));
rcv.setLayoutManager(linearLayoutManager);
```

### 4.2. onBindViewHolder 내에서 setOnClickListener 사용 자제

onBindViewHolder는 아이템 하나하나마다 실행되며 데이터를 뷰에 설정하는 함수이다.

이 때, onBindViewHolder에 setOnClickListener를 넣게 되면 실행될때마다 setOn을 하여 렉을 유발할 수 있다.

대신, setOnClickListener는 ViewHolder의 생성자에서 설정해주자.

position은 getAdapterPosition() 함수를 사용하여 얻으면 된다.

### 4.3. 데이터 새로고침

데이터 새로고침의 함수는 여러가지가 있다.

notifyDataSetChanged() : 데이터 전체를 새로고침한다. 전체값이 바뀌는 경우가 아니면 사용을 자제하자.

notifyItemChanged( *int position* ) : 해당 position을 새로고침한다.

notifyItemRangeChanged( *int positionStart*, *int itemCount* ) : positionStart 위치부터 itemCount 만큼 새로고침한다.

notifyItemRemoved( *int position* ) : 해당 position의 itemView를 삭제한다. item이 삭제되는 애니메이션 효과가 발생한다.

notifyDataSetChanged() 를 통해 전부 새로고침하는 것보다, 다른 함수를 사용하여 새로고침하는 것이 효율적이다.

&nbsp;

## 5. 오류 해결방법

### 5.1. item이 하나만 보인다면

item xml 파일에서 최상위 view의 layout_height를 wrap_content 로 바꿔주자.

item이 화면을 가득 채워서 하나만 보이는 경우이다.