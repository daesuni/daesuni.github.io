---
layout: post
title: JDBC로 Postgres 연결하기
category: Java
---

package kr.co.wips.wpan;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Migration {

	@SuppressWarnings("resource")
	public static void main(String[] args) throws Exception {

		Statement s = null;
		PreparedStatement ps = null;
		ResultSet rs = null;

		try (Connection conn = DriverManager.getConnection("jdbc:postgresql://xxx.xxx.xxx.xxx:5432/wpan", "id", "password")) {

			// 기존 데이터 삭제
			s = conn.createStatement();
			s.addBatch("DELETE FROM tablename");
        	s.executeBatch();

        	// 기존 리스트 가져오기
            List<Map<String, Object>> docList = null;
            ps = conn.prepareStatement("SELECT col1, col2 FROM tablename");
            rs = ps.executeQuery();
            docList = rsConvertor(rs);

            s = conn.createStatement();
            for (int i = 0; i < docList.size(); i++) {
            	if (i > 0 && i % 100 == 0) {
            		s.executeBatch();
            		s = conn.createStatement();
            	};

            	System.out.println("--- " + (i + 1) + "/" + docList.size() + " ---");

            	Map<String, Object> doc = docList.get(i);

				ps = conn.prepareStatement("INSERT INTO tablename(col1, col2) VALUES (?, ?)");
				ps.setString(1, doc.get("col1").toString());
            	ps.setString(2, doc.get("col2").toString());
            	s.addBatch(ps.toString());

            	if (i + 1 == docList.size()) s.executeBatch();
			};
			ps.close();
			s.close();
			conn.close();
        } catch (SQLException e) {
            System.out.println("Connection failure.");
            e.printStackTrace();
        }
	}

	// resultset > list<map> convert
	public static List<Map<String, Object>> rsConvertor(ResultSet rs) throws Exception {
        ResultSetMetaData metaData = rs.getMetaData();
        int sizeOfColumn = metaData.getColumnCount();
        List<Map<String, Object>> list = new ArrayList<>();
        Map<String, Object> map;
        String column;
        while (rs.next()) {
            map = new HashMap<String, Object>();
            for (int indexOfcolumn = 0; indexOfcolumn < sizeOfColumn; indexOfcolumn++) {
                column = metaData.getColumnName(indexOfcolumn + 1);
                map.put(column, rs.getString(column));
            }
            list.add(map);
        }
        return list;
    }
}
