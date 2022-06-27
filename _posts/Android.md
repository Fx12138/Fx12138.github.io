---
title: Android
date: 2021-04-16 13:39:33
tags: Android
---

最近写了一个viewpaper+fragment实现底部导航的页面,记录一下

整体的效果大概是这样,通过页面下方的导航栏可以进行页面的跳转

<img src="D:\devApp\blog\source\_posts\Android.assets\image-20210416134643199.png" alt="image-20210416134643199" style="zoom:50%;" /><img src="D:\devApp\blog\source\_posts\Android.assets\image-20210416134737687.png" alt="image-20210416134737687" style="zoom:50%;" />

## 1导航界面

首先实现viewpaper,在activity_layout.xml中设计导航页面

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal"
    android:background="#FFFFFF"
    tools:context=".zhihui.ZhihuiActivity">
    <RadioGroup
        android:id="@+id/main_radio_group"
        android:layout_width="match_parent"
        android:layout_height="49dp"
        android:layout_alignParentBottom="true"
        android:background="@color/white"
        android:gravity="center"
        android:orientation="horizontal">

        <RadioButton
            android:layout_width="5dp"
            android:layout_height="match_parent"
            android:drawableTop="@drawable/mine"
            android:layout_marginTop="11dp"
            android:layout_marginBottom="5dp"
            android:layout_weight="1"
            android:button="@null"
            android:text="门户"
            android:gravity="center"
            android:textSize="10sp"
            android:textColor="#262626"
            android:background="@color/white"
            android:id="@+id/rbMenhu"
            ></RadioButton>

        <RadioButton
            android:id="@+id/rbBaojing"
            android:layout_width="5dp"
            android:layout_height="match_parent"
            android:drawableTop="@drawable/mine"
            android:layout_marginTop="11dp"
            android:layout_marginBottom="5dp"
            android:layout_weight="1"
            android:gravity="center"
            android:textSize="10sp"
            android:textColor="#262626"
            android:text="报警"
            android:background="@color/white"
            android:button="@null"></RadioButton>
        <RadioButton
            android:id="@+id/rbZhihui"
            android:layout_width="5dp"
            android:layout_height="match_parent"
            android:layout_marginTop="11dp"
            android:layout_marginBottom="5dp"
            android:drawableTop="@drawable/mine"
            android:layout_weight="1"
            android:gravity="center"
            android:textSize="10sp"
            android:textColor="#262626"
            android:text="智慧"
            android:background="@color/white"
            android:button="@null"></RadioButton>

        <RadioButton
            android:id="@+id/rbWode"
            android:layout_width="5dp"
            android:layout_height="match_parent"
            android:layout_marginTop="11dp"
            android:layout_marginBottom="5dp"
            android:layout_weight="1"
            android:drawableTop="@drawable/mine"
            android:button="@null"
            android:gravity="center"
            android:textSize="10sp"
            android:textColor="#262626"
            android:background="@color/white"
            android:text="我的"
            ></RadioButton>
    </RadioGroup>
    <androidx.viewpager.widget.ViewPager
        android:id="@+id/main_viewpager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_above="@id/main_radio_group"></androidx.viewpager.widget.ViewPager>

</RelativeLayout>
```

## 2viewpaper的适配器

```java
public class MyFragmentPaperAdapter extends FragmentPagerAdapter {


    private int PAGE_NUM = 3;
    private final int PAGE_MENHU = 0;
    private final int PAGE_BAOJING = 1;
    private final int PAGE_ZHIHUI = 2;
    private final int PAGE_WODE = 3;
    private MenhuFragment menhuFragment = null;
    private BaojingFragment baojingFragment = null;
    private ZhihuiFragment zhihuiFragment = null;
    private WodeFragment wodeFragment = null;

    public MyFragmentPaperAdapter(@NonNull FragmentManager fm) {
        super(fm);
        menhuFragment = new MenhuFragment();
        baojingFragment = new BaojingFragment();
        zhihuiFragment = new ZhihuiFragment();
        wodeFragment = new WodeFragment();
    }

    @NonNull
    @Override
    public Fragment getItem(int position) {
        switch (position) {
            case PAGE_MENHU: {
                return menhuFragment;
            }
            case PAGE_BAOJING: {
                return baojingFragment;
            }
            case PAGE_ZHIHUI: {
                return zhihuiFragment;
            }
            case PAGE_WODE: {
                return wodeFragment;
            }
            default:
                return null;
        }
    }

    @Override
    public int getCount() {
        return PAGE_NUM;
    }
}
```

## 3fragment的layout文件

创建几个fragment的布局文件,我这里创建的是fragment_baojing.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="报警界面"
        android:textColor="@color/black">

    </TextView>

</LinearLayout>
```

由于四个都很类似,我就不再一一堆代码了

## 4对fragment进行实现

创建BaojingFragment类继承Fragment类,重写onCreatView方法

```java
public class BaojingFragment extends Fragment {
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_baojing, container,false);
        return view;
    }
}
```

对自己定义的几个fragment都进行这样的实现

## 5activity

```java
public class ZhihuiActivity extends BaseActivity implements ViewPager.OnPageChangeListener, RadioGroup.OnCheckedChangeListener{

    private MyFragmentPaperAdapter myFragPageAdapter;
    private ViewPager viewPager;
    private RadioGroup radioGroup;
    private RadioButton radioButton_menhu;
    private RadioButton radioButton_baojing;
    private RadioButton radioButton_zhihui;
    private RadioButton radioButton_wode;
    private int PAGE_NUM = 3;
    private final int PAGE_MENHU = 0;
    private final int PAGE_BAOJING = 1;
    private final int PAGE_ZHIHUI = 2;
    private final int PAGE_WODE = 3;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_zhihui);

        myFragPageAdapter=new MyFragmentPaperAdapter(getSupportFragmentManager());
        bindViews();
        radioButton_zhihui.setChecked(true);
    }


    private void bindViews(){
        radioButton_menhu=findViewById(R.id.rbMenhu);
        radioButton_baojing=findViewById(R.id.rbBaojing);
        radioButton_zhihui=findViewById(R.id.rbZhihui);
        radioButton_wode=findViewById(R.id.rbWode);

        radioGroup=findViewById(R.id.main_radio_group);
        radioGroup.setOnCheckedChangeListener(this);
        viewPager= findViewById(R.id.main_viewpager);
        viewPager.setAdapter(myFragPageAdapter);
        viewPager.addOnPageChangeListener(this);
        viewPager.setCurrentItem(0);
    }

    @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

    }

    @Override
    public void onPageSelected(int position) {

    }

    @Override
    public void onPageScrollStateChanged(int state) {
        if (state==2){
            switch (viewPager.getCurrentItem()){
                case PAGE_MENHU:
                    radioButton_menhu.setChecked(true);
                    break;
                case PAGE_BAOJING:
                    radioButton_baojing.setChecked(true);
                    break;
                case PAGE_ZHIHUI:
                    radioButton_zhihui.setChecked(true);
                    break;
                case PAGE_WODE:
                    radioButton_wode.setChecked(true);
                    break;

            }
        }
    }

    @Override
    public void onCheckedChanged(RadioGroup group, int checkedId) {
        switch (checkedId){
            case R.id.rbMenhu:{
                viewPager.setCurrentItem(PAGE_MENHU);
                break;
            }
            case R.id.rbBaojing:{
                viewPager.setCurrentItem(PAGE_BAOJING);
                break;
            }
            case R.id.rbZhihui:{
                viewPager.setCurrentItem(PAGE_ZHIHUI);
                break;
            }
            case R.id.rbWode:{
                viewPager.setCurrentItem(PAGE_WODE);
                break;
            }
        }
    }

}
```

# ListView

主活动的layout

```xml
<LinearLayout android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    xmlns:android="http://schemas.android.com/apk/res/android">

    <ListView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/lv_one">

    </ListView>

</LinearLayout>
```

listview的布局

```xml
<LinearLayout android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    xmlns:android="http://schemas.android.com/apk/res/android">

   <TextView
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:id="@+id/lv_item">

   </TextView>

</LinearLayout>
```

实体类Bean

```java
public class Bean {
    String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

创建adapter

```java
public class MyAdapter extends BaseAdapter {

    private List<Bean> data;
    private Context context;

    public MyAdapter(List<Bean> data, Context context) {
        this.data = data;
        this.context = context;
    }

    @Override
    public int getCount() {
        return data.size();
    }

    @Override
    public Object getItem(int position) {
        return null;
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        if(convertView == null){
            convertView = LayoutInflater.from(context).inflate(R.layout.lv_one,parent,false);
        }
        TextView textView = convertView.findViewById(R.id.lv_item);
        textView.setText(data.get(position).getName());
        return convertView;
    }
}

```

主活动

```java
public class MainActivity extends AppCompatActivity {
    private List<Bean> data = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        for(int i = 0;i<100;i++){
            Bean bean = new Bean();
            bean.setName("上官婉儿"+i);
            data.add(bean);
        }

        ListView listView = findViewById(R.id.lv_one);
        listView.setAdapter(new MyAdapter(data,this));

    }
}
```

