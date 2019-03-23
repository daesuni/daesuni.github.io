---
layout: post
title: Pgsql 자주쓰는 쿼리 모음
category: Postgres
---

### 1. Java back-paging



select
	array_to_string(array_agg(t.rword), ',')
FROM(
	SELECT
		r.i,
		r.rword
	FROM (
	SELECT
		row_number() OVER (
			ORDER BY cnt DESC
		) AS i,

		LOWER(rword) AS rword,

		cnt
	FROM
		new_srch_frmla_20181108_simil_word
	WHERE

		removed = false
		) as r
	where
		r.i > 125000
		and r.i <= 130000
) as t




SELECT
	count(*)
FROM
	new_srch_frmla_20181108_raw_srch_item
WHERE
	lower(srch_frmla)
similar to
	'%(heat|detect|light|control|water|cover|검출|connect|air|cell|분리|결합|회전|hole|screen|power|press|support|pipe|wire)%';
