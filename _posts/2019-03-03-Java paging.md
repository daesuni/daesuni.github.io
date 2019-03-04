---
layout: post
title: Java paging & Javascript paging
category: Java
---

대용량 데이터 처리에 있어서 페이징처리는 중요하다. 브라우저가 담을 수 있는 데이터는 한계가 있고, 서버가 데이터를 가져오는 속도의 한계 때문이다. 대용량일지라도 페이징처리를 하면 front는 물론 back에서도 처리속도와 처리용량에 큰 이득을 볼 수 있다. 페이징 처리에는 front에서 또는 back에서 처리가 가능한데, 두 경우에 특징이 명확하다.

Front에서 페이징 처리를 하는 경우는 모든 데이터를 한꺼번에 가져와서 client에서 처리를 하기 때문에 초기 로딩속도는 느린 반면, 이후 페이징 시 서버와 통신을 하지 않아도 되어 빠른 속도를 보여준다. 또한 스크립트 검색이 가능하다는 장점도 있다.

Back에서 페이징 처리를 하는 경우, 각 페이지마다 서버를 통해 해당하는 데이터만 가져오게되므로 모든 페이지의 로딩속도가 거의 균일하다고 할 수 있다. 당연히 client의 부담도 줄어들게 되지만, 검색의 경우 전체 데이터를 대상으로 실시간으로 적용하기에는 무리가 있다.

이처럼 두가지 페이징 방식에 따라 trade-off해야 할 상황이 다르기 때문에 어떤 상황에서 어떤 페이징 처리를 할 것인지 고민을 해야 한다. 이번 포스팅에서는 Java를 이용한 back-paging방식과 Javascript/Jquery를 이용한 front-paging의 대표적인 예를 하나씩 살펴볼 것이다.

### 1. Java back-paging

먼저 paging을 구현해 줄 class를 만들어 준다.

```java
public class Paging {

  private int firstPageNo; // 첫 번째 페이지 번호
  private int prevPageNo; // 이전 페이지 번호
  private int startPageNo; // 시작 페이지 (페이징 네비 기준)
  private int pageNo; // 페이지 번호
  private int endPageNo; // 끝 페이지 (페이징 네비 기준)
  private int nextPageNo; // 다음 페이지 번호
  private int finalPageNo; // 마지막 페이지 번호
  private int totalCount; // 게시 글 전체 수
  private int pageSize; // 게시 글 수
  private int startSeq; // 게시글 시작 시퀀스
  private int endSeq; // 게시글 끝 시퀀스

  /**
  * @return the pageSize
  */
  public int getPageSize() {
    return pageSize;
  }
  /**
  * @return the firstPageNo
  */
  public int getFirstPageNo() {
    return firstPageNo;
  }
  /**
  * @return the prevPageNo
  */
  public int getPrevPageNo() {
    return prevPageNo;
  }
  /**
  * @return the startPageNo
  */
  public int getStartPageNo() {
    return startPageNo;
  }
  /**
  * @return the pageNo
  */
  public int getPageNo() {
    return pageNo;
  }
  /**
  * @return the endPageNo
  */
  public int getEndPageNo() {
    return endPageNo;
  }
  /**
  * @return the nextPageNo
  */
  public int getNextPageNo() {
    return nextPageNo;
  }
  /**
  * @return the finalPageNo
  */
  public int getFinalPageNo() {
    return finalPageNo;
  }
  /**
  * @return the totalCount
  */
  public int getTotalCount() {
    return totalCount;
  }
  /**
  * @param pageSize the pageSize to set
  */
  public void setPageSize(int pageSize) {
    this.pageSize = pageSize;
  }
  /**
  * @param firstPageNo the firstPageNo to set
  */
  public void setFirstPageNo(int firstPageNo) {
    this.firstPageNo = firstPageNo;
  }
  /**
  * @param prevPageNo the prevPageNo to set
  */
  public void setPrevPageNo(int prevPageNo) {
    this.prevPageNo = prevPageNo;
  }
  /**
  * @param startPageNo the startPageNo to set
  */
  public void setStartPageNo(int startPageNo) {
    this.startPageNo = startPageNo;
  }
  /**
  * @param pageNo the pageNo to set
  */
  public void setPageNo(int pageNo) {
    this.pageNo = pageNo;
  }
  /**
  * @param endPageNo the endPageNo to set
  */
  public void setEndPageNo(int endPageNo) {
    this.endPageNo = endPageNo;
  }
  /**
  * @param nextPageNo the nextPageNo to set
  */
  public void setNextPageNo(int nextPageNo) {
    this.nextPageNo = nextPageNo;
  }
  /**
  * @param finalPageNo the finalPageNo to set
  */
  public void setFinalPageNo(int finalPageNo) {
    this.finalPageNo = finalPageNo;
  }
  /**
  * @param totalCount the totalCount to set
  */
  public void setTotalCount(int totalCount) {
    this.totalCount = totalCount;
    this.makePaging();
  }
  /**
  * @return the startSeq
  */
  public int getStartSeq() {
    return startSeq;
  }
  /**
  * @return the endSeq
  */
  public int getEndSeq() {
    return endSeq;
  }
  /**
  * @param startSeq the startSeq to set
  */
  public void setStartSeq(int startSeq) {
    this.startSeq = startSeq;
  }
  /**
  * @param endSeq the endSeq to set
  */
  public void setEndSeq(int endSeq) {
    this.endSeq = endSeq;
  }

  // 페이징 생성
  private void makePaging() {
    if (this.totalCount == 0) return; // 카운트 미지정시
    if (this.pageNo == 0) this.setPageNo(1); // 기본은 첫 페이지
    if (this.pageSize == 0) this.setPageSize(10); // 기본 페이지당 수는 10

    int finalPage = (totalCount + (pageSize - 1)) / pageSize; // 마지막 페이지
    if (this.pageNo > finalPage) this.setPageNo(finalPage); // 마지막 페이지를 넘을 경우

    if (this.pageNo < 0 || this.pageNo > finalPage) this.pageNo = 1; // 페이지 유효성 체크

    boolean isNowFirst = pageNo == 1 ? true : false; // 시작 페이지 (전체)
    boolean isNowFinal = pageNo == finalPage ? true : false; // 마지막 페이지 (전체)

    int startPage = ((pageNo - 1) / 10) * 10 + 1; // 시작 페이지 (페이징 네비 기준)
    int endPage = startPage + 10 - 1; // 끝 페이지 (페이징 네비 기준)

    if (endPage > finalPage) { // [마지막 페이지 (페이징 네비 기준) > 마지막 페이지] 보다 큰 경우
        endPage = finalPage;
    }

    this.setFirstPageNo(1); // 첫 번째 페이지 번호

    if (isNowFirst) {
        this.setPrevPageNo(1); // 이전 페이지 번호
    } else {
        this.setPrevPageNo(((pageNo - 1) < 1 ? 1 : (pageNo - 1))); // 이전 페이지 번호
    }

    this.setStartPageNo(startPage); // 시작 페이지 (페이징 네비 기준)
    this.setEndPageNo(endPage); // 끝 페이지 (페이징 네비 기준)

    if (isNowFinal) {
        this.setNextPageNo(finalPage); // 다음 페이지 번호
    } else {
        this.setNextPageNo(((pageNo + 1) > finalPage ? finalPage : (pageNo + 1))); // 다음 페이지 번호
    }

    int startSeq = (pageNo - 1) * pageSize;
    int endSeq = (pageNo * pageSize);
    this.setStartSeq(startSeq); // 게시글 시작 시퀀스
    this.setEndSeq(endSeq); // 게시글 끝 시퀀스

    this.setFinalPageNo(finalPage); // 마지막 페이지 번호
  }
}
```

서버에서 사용할 때는 다음 예와 같이 페이징 객체를 만들고, 입맛에 따라 수정사용하면 된다.

```java
Paging paging = new Paging();
paging.setPageNo(1);
paging.setPageSize(20);
paging.setTotalCount(500);

Integer start = paging.getStartSeq();
Integer end = paging.getEndSeq();
```

시작 글 번호와 끝 글 번호를 구해서 sql문의 where조건에 걸어주어도 되고, 경우에 따라 offset을 이용할 수도 있다.

### 2. Javascript/Jquery front-paging

```html
<div id="paging"></div>
```

```javascript
function paging(page) {

  var totalData = 1000;    // 총 데이터 수
  var dataPerPage = 20;    // 한 페이지에 나타낼 데이터 수
  var pageCount = 10;      // 한 화면에 나타낼 페이지 수

  console.log('currentPage : ' + page);

  var totalPage = Math.ceil(totalData / dataPerPage);    // 총 페이지 수
  var pageGroup = Math.ceil(page / pageCount);    // 페이지 그룹

  console.log('pageGroup : ' + pageGroup);

  var last = pageGroup * pageCount;    // 화면에 보여질 마지막 페이지 번호
  if (last > totalPage) last = totalPage;
  var first = last - (pageCount - 1);    // 화면에 보여질 첫번째 페이지 번호
  var next = last ++;
  var prev = first --;

  console.log('last : ' + last);
  console.log('first : ' + first);
  console.log('next : ' + next);
  console.log('prev : ' + prev);

  var $pingingView = $('#paging');

  var html = '';

  if (prev > 0) html += '<a href=# id="prev"><</a> ';

  for (var i = first; i <= last; i++) {
      html += '<a href="#" id=' + i + '>' + i + '</a> ';
  };

  if (last < totalPage) html += '<a href=# id="next">></a>';

  $('#paging').html(html);    // 페이지 목록 생성
  $('#paging a').css('color', 'black');
  $('#paging a#' + page).addClass('active'); // 현재 페이지 표시

  $('#paging a').click(function(){
      var $item = $(this);
      var $id = $item.attr('id');
      var selectedPage = $item.text();

      if ($id == 'next') selectedPage = next;
      if ($id == 'prev') selectedPage = prev;

      paging(selectedPage);
  });
}

$('document').ready(function(){
  paging(1);
});
```

##### 참고

본 포스팅은 아래 두 분의 블로그의 소스를 참고하여 수정했습니다.

- https://blog.whitelife.co.kr/215
- https://sjh010.tistory.com/1
